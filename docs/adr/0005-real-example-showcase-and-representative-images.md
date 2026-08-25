# ADR-0005: Real competitor-example showcase & representative-image policy

## Status

Accepted (PoC Revision 5)

## Context

PoC Revision 5 requires the app to demonstrate, "one real example for the demo for each competitor", in the order of the Core Competition list: **motors.co.uk, AutoTrader, Cazoo, Gumtree, Facebook Marketplace**. The product's long-term ambition is harvesting competitor listings into one Pine It feed.

Two questions had to be answered before coding:

1. **What is "real"?** Individual third-party listing pages are ephemeral (auto-renew, sell, 410/404) — and most platforms gate content behind JavaScript or login (Facebook Marketplace especially). The PoC is a **static single file** (ADR-0001), so live fetching is not an option anyway.
2. **Can we display competitors' images?** The team wants the Real product to show at least one image per listing **without paying for any licence and without violating platform ToS** — "maybe a free stock library", "maybe embed the ad", "screenshot it ourselves".

The screenshot idea is legally a non-runner. In *Trader Corp. v. CarGurus*, 2017 ONSC 1841 (Canada), CarGurus scraped/hotlinked dealer and realtor photographs, argued it was "just a search engine/linking", and lost: the court rejected the information-location/search-engine defence for framing and hotlinking and awarded statutory damages ($305,064; ~$2/photo). Screenshotting, downloading, hotlinking or iframing someone else's photograph are all forms of reproduction/communication that **do not create a new copyright** — the owner remains the photographer (or the platform that is licensed the photo). Framing was *specifically* rejected in that case. Heat-checking live terms confirmed:

| Platform | What its terms actually allow | Bottom line |
|---|---|---|
| AutoTrader | Search results "only use, reproduce and print… for your own personal, non-commercial purposes"; Connect API is licensed to advertisers/integrators via direct agreement | No third-party image display licence; licensed feed is the real path |
| motors.co.uk / Cazoo | Same legal entity — "Cazoo is a trading name of Motors.co.uk Limited" (brand owned since 27 Jun 2024); both "All rights reserved", no reuse grant | Same-entity note; licensed feed is the real path |
| Gumtree | Grants Gumtree itself non-exclusive rights to *user* content; grants **no third-party** licence to reproduce/display listings or images | No reuse grant to us |
| Facebook Marketplace | No marketplace API; items are account/login-gated | Cannot legally aggregate items; cross-post/affiliate is the honest path |

**Conclusion:** the copyright-safe, licence-free, ToS-safe way to show "at least one image" is to show **our own representative image** (illustration or CC0/CC photo of the make/model) beside the **factual spec data** and a **deep link** to the real listing. Facts (make, model, year, price, mileage, fuel) are largely factual data; the *photograph* is the copyrighted artefact we must not copy. Users stay in Pine It to browse/compare, and deep-link out to view the real photos and transact.

## Decision

1. **Ship exactly one real-example listing per competitor**, in the order Motors.co.uk → AutoTrader → Cazoo → Gumtree → Facebook Marketplace, as **point-in-time snapshots** pinned in the static data (`REAL_EXAMPLES` inside `docs/index.html`). Each carries:
   - the **real factual listing data** (captured from live pages at build time);
   - a **real deep-link URL** to the platform (with the existing `?utm_source=pineit&utm_medium=referral&aff_id={AFF_ID}` affiliate placeholder per the production-readiness guardrails);
   - `realListing:true` so the UI can badge it.
2. **No competitor photograph is stored or hotlinked.** Every card (real-example and seeded) renders **our own representative image** — the deterministic car illustration already keyed by make/model/colour in `svgCar()` — labelled **"Representative image"**. A production build may upgrade this to a mapped set of self-owned or CC0/CC photos per make/model via the same `representativeArt` seam, still without copying competitor files.
3. **Cazoo is a first-class source.** Added to the source registry (`SOURCES`) so badge, colour, facet group and CTA derive automatically (ADR-0002). The fact that it shares one legal entity with motors.co.uk is noted in docs, not hidden.
4. **Facebook Marketplace is the honest exception.** No item-level deep link exists behind the login wall, so the example is a clearly-labelled representative Marketplace car with a deep link to the Marketplace *area* and an inline "Facebook login required" note. Production options: skip FB, or an **opt-in cross-post** ("list this on Pine It too") where the seller publishes in both places.
5. **Mobile app routing.** Each source registers an `appScheme` (e.g. `autotrader://`, `gumtree://`, `fb://marketplace`, `cazoo://`, `motors://`). The "View on [platform]" action runs `openCompetitor()`: on a coarse-pointer (mobile) device it attempts the app scheme and falls back to the https URL (a Universal/App Link) — installed app wins, otherwise the default browser. Desktop just opens the https URL in a new tab.
6. **Production is a licensed-feed aggregator, not a scraper.** Row 2 of `docs/PoC-PRODUCTION-READINESS.md` moves from MOCK to *real demo examples + licensed-feed plan*. The real paths are official feeds/APIs — AutoTrader Connect (or a direct data agreement) and the Motors/Gumtree dealer DMS stock feeds — plus an opt-in cross-post for Facebook. Scraping (including hotlinking) is out of scope in every case.

## Alternatives considered

| # | Alternative | Why rejected / deferred |
|---|---|---|
| 1 | Screenshot/copy the listing photo ("then it's our image") | Creates no new copyright; outright infringement (Trader v CarGurus). Rejected. |
| 2 | Hotlink the main photo and credit the source | Displaying a copy is infringement regardless of attribution; attribution consents but grants no licence; the case law rejects the "linking = fair use" defence for aggregators. Rejected for production. |
| 3 | iframe/embed the ad | Framing was rejected as a safe harbour in Trader v CarGurus; platforms send `X-Frame-Options`/CSP `frame-ancestors` and ToS forbid it. Rejected. |
| 4 | Live server-side fetch of listings in the PoC | Violates ADR-0001 (static, zero-fetch, file://-runnable) and listing URLs expire. Deferred; feed ingestion belongs to the licensed-partner phase. |
| 5 | Show only a neutral placeholder + deep-link only | Safe, but a generic placeholder hurts the "we surface competitors" demo; the representative make/model illustration gives context at zero legal cost. Accepted as a *fallback* tier rather than the whole design. |
| 6 | Buy a commercial vehicle-image API (CarImages/CarsXE etc.) | Adds recurring cost, contradicts the "pay nothing" constraint. Noted as an optional higher-realism tier; the representative-image seam can adopt it later. |

## Consequences

**Positive:** the PoC demonstrates genuine cross-platform surfacing (real facts + real deep links + per-source app routing) with zero licence cost and zero ToS exposure; images are ours; Cazoo becomes a first-class facet; the demo works offline and will not break when a listing page 404s (representative image is guaranteed); the enforcement risk is documented instead of ignored.

**Negative:** the demo doesn't show the competitor's actual photograph, so reviewers must be told the photos are representative (hence the **"Representative image"** badge on every real-example card). Real examples are snapshots — a referenced listing may have sold; the UI labels them **"snapshot"** and the docs state the provenance.

**Neutral:** the production launch still requires partner/affiliate agreements (data feeds, DMS integrations, affiliate account params to replace `{AFF_ID}`); this ADR fixes the *image* policy so no scraping path is inferred.

## Links / references

- `docs/index.html` → `SOURCES`, `REAL_EXAMPLES`, `openCompetitor()`, `renderShowcase()`.
- ADR-0001 (static PoC), ADR-0002 (source registry), ADR-0003 (provider-agnostic data), `docs/PoC-PRODUCTION-READINESS.md`.
- Legal note: *Trader Corp. v. CarGurus*, 2017 ONSC 1841 (CanLII) — hotlinking/framing of listing photos is infringing for an aggregator; search-engine-style "links only" defence rejected.