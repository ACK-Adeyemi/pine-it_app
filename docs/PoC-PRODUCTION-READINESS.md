# PoC Production-Readiness — Pine It v0.1.0

> Explicit inventory of what is **real**, what is **mocked/stubbed** in the single-file PoC, and the **planned real integration** for each. This document exists so nobody mistakes a mock for a production capability, and so the v1 build has a precise shopping list. Mitigations: specifics (ToS, affiliate terms, IIS/parties) must be confirmed before production.

---

## Integration inventory

| # | Capability | PoC implementation | PoC status | Planned real integration |
|---|---|---|---|---|
| 1 | **Listings catalogue** | ~700 deterministic in-code seed listings (prng) **+ 5 pinned real-example snapshots** (Rev 5) | MOCK + real demos | Real listing service (native posts + imported market feed) |
| 2 | **Harvested platforms** (Gumtree, FB Marketplace, AutoTrader, motors.co.uk, **Cazoo**) | **5 pinned real-example snapshots** (one per platform: real factual listing data + deep link); deep links are **verified live single-ad PDPs** for motors.co.uk (`car-79576221`), AutoTrader (`car-details/202606012893471`), Gumtree (`/p/…/1802012169`) and Cazoo (`cars-for-sale/79613297`, owner-supplied URL + owner-transcribed figures); FB remains area-level with a login-wall note (v0.2.4) | DEMO SNAPSHOTS + STUB (licensed feeds planned) | Licensed feeds/APIs per platform — AutoTrader Connect or direct data agreement, Motors/Gumtree dealer DMS stock feeds, opt-in FB cross-post; original-uploader attribution + affiliate terms. Scraping/hotlinking is out of scope (Trader v CarGurus). |
| 3 | **Affiliate/outbound links** | URLs built with `?utm_source=pineit…&aff_id={AFF}` placeholder params; open in new tab | STUB | Real affiliate-account params per programme |
| 4 | **Lead / broker flow** ("Request via Pine It") | Local form → `localStorage['pineit_leads']`; confirmation + re-entry in "My activity" | MOCK | Lead capture API → routing/queue → seller contact via SMS/email/phone; broker commission tracking if middleman model |
| 5 | **Auth / account wall** | None — anonymous browse, post and lead are all public | N/A (deliberate) | Optional OIDC later; PoC proves no account wall is viable — keep it |
| 6 | **Post an ad** | Full car form incl. optional **"Specification & running details"** section mapping every filter facet (Rev 6) → `localStorage['pineit_my_ads']`; appears live as **Native**, re-renders; blanks auto-filled deterministically | MOCK (local persistence) | Real listing service with moderation, image upload |
| 7 | **Running-state classification** ("Spares & Repairs" / "Non Starter") | Deterministic client-side **phrase list** (`runningStatus()` over title+description+condition, precedence Non Starter > Spares & Repairs > Running) **+ explicit seller selection in the post form which always wins**; badges + default-hidden-until-filtered feed gate (Rev 6, ADR-0006) | REAL (in-PoC logic) | Server-side ML/LLM assist to enrich harvested-feed text before classification; DVLA/V5 condition fields for verified data |
| 7 | **Persistence & export/import** | `localStorage` + JSON contract (see DATA-MODEL §6); both read/write lossless | MOCK | Selected provider + `exportDataset()/importDataset()` equivalents (ADR-0003, ADR-0004 pending) |
| 8 | **Geolocation / radius** | Postcode hashed to a deterministic pseudo-co-ordinate in the UK bbox (haversine distance) | MOCK | Real postcode→lat/lng geocoder + radius search (server-side for v1 volumes) |
| 9 | **Images / photos** | **Representative own illustrations** (`svgCar()` per make/colour), labelled "Representative image"; no competitor photograph is stored or hotlinked (ADR-0005) | REAL for own images | Real image CDN + upload pipeline for native ads; licensed feeds for harvested ads |
| 10 | **Extended filter attributes** (seats, ULEZ, running costs, drivetrain, BHP, EV/hybrid, towing, features, safety, keys, MOT, history, usage, dealer, advert options) | Deterministic seeded `attrs` on all listings (correlated with fuel/body/age) | MOCK | Real DVLA / vehicle-data provider and dealer metadata feeds; unit config per ADR-0004 |
| 10 | **Search backend** | Client-side `Array.filter` over facets | REAL (for scale=700) | Facet-capable search tier (Elasticsearch/Solr/Typesense) when listings > ~10k |
| 11 | **Hosting / delivery** | GitHub Pages from `/docs`, zero build static | REAL | Real CDN (Cloudflare/Azure CDN), app aliases, https |
| 12 | **Analytics** | none (console logging only) | NONE | Privacy-first web analytics (no cross-site) |
| 13 | **State sharing (URL hash)** | Hash encodes filters; restored on load; tests live | REAL | Same URL pattern maintained |
| 14 | **Advertising assets** (nav banner, inline banner rows, sponsored embeds) | All content/visibility from external **`docs/ad-config.json`** + built-in defaults; deterministic placement (banner every 9 cards, sponsored every 10); 3-way sponsored mode (paid / default Pine It promos / off); slots labelled; demo CTA links route in-app | MOCK (sample paid items point at `example.com`) | Real ad server / ad-manager integration (e.g. GAM/AdSense or direct-sold inventory), ad ops workflow, frequency capping, viewability + privacy-compliant measurement |
| 15 | **Admin console & moderation** | Header **Admin** button (no login); banners tab with **Download ad-config.json** publish flow; ads moderation (hide/restore/delete posted ads); accounts moderation (posters by optional display name + lead requesters by name/email; block suppresses content at render time) → `localStorage['pineit_moderation']` | MOCK (device-local, demo-grade) | **Separate auth-protected console URL location** (e.g. `admin.<domain>` or `/console`) with roles, server-side moderation flags + audit trails, real account/user IDs, and DB-backed queues — never shipped inside the marketplace bundle |

---

## Mock boundary guardrails (hard rules)

1. No capability marked **MOCK/STUB** may be represented as real in demos, labels, or pitch material.
2. `localStorage` data is explicitly **per-browser, per-device** — wiping it is not data loss for the product.
3. All outbound "affiliate" URLs must carry `{AFF}` placeholder semantics until a real programme exists — no undisclosed affiliate links before terms are confirmed.
4. Before any real scraping is built: ToS/legal review per platform; robots.txt; take-down on request; collection/dpa filing as needed.

## First-production milestones (feeding v1 planning)

1. Decide the persistence provider against the import/export contract → **ADR-0004**
2. Pick first harvested platform partner/feed — likely Automotive Trader or Gumtree API → data-sharing ToS
3. Affiliate/lead model: confirm affiliate programmes OR validate **broker** unit economics for `brokered` sources
4. Real postcode geocoding (UK Ordinance Survey / postcodes.io)
5. Image upload pipeline + moderation
6. Advertising stack: pick ad server vs direct sales, move banner/sponsored config to a CMS-served JSON (or DB), and build the **separate auth-protected console URL** replacing the PoC admin overlay (ADR-0007)

---

*Companion docs:* `PRD.md` · `DATA-MODEL.md` · `docs/adr/0001–0007`