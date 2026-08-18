# PoC Production-Readiness — Pine It v0.1.0

> Explicit inventory of what is **real**, what is **mocked/stubbed** in the single-file PoC, and the **planned real integration** for each. This document exists so nobody mistakes a mock for a production capability, and so the v1 build has a precise shopping list. Mitigations: specifics (ToS, affiliate terms, IIS/parties) must be confirmed before production.

---

## Integration inventory

| # | Capability | PoC implementation | PoC status | Planned real integration |
|---|---|---|---|---|
| 1 | **Listings catalogue** | ~700 deterministic in-code seed listings (prng) | MOCK | Real listing service (native posts + imported market feed) |
| 2 | **Harvested platforms** (Gumtree, FB Marketplace, AutoTrader, motors.co.uk) | Statistics in seed data; source registry drives labels/URLs/keywords | MOCK | Scrapers/partnerships/feeds per platform, legality-reviewed + original-uploader attribution (?utm affiliate terms) |
| 3 | **Affiliate/outbound links** | URLs built with `?utm_source=pineit…&aff_id={AFF}` placeholder params; open in new tab | STUB | Real affiliate-account params per programme |
| 4 | **Lead / broker flow** ("Request via Pine It") | Local form → `localStorage['pineit_leads']`; confirmation + re-entry in "My activity" | MOCK | Lead capture API → routing/queue → seller contact via SMS/email/phone; broker commission tracking if middleman model |
| 5 | **Auth / account wall** | None — anonymous browse, post and lead are all public | N/A (deliberate) | Optional OIDC later; PoC proves no account wall is viable — keep it |
| 6 | **Post an ad** | Full car form → `localStorage['pineit_my_ads']`; appears live as **Native**, re-renders | MOCK (local persistence) | Real listing service with moderation, image upload |
| 7 | **Persistence & export/import** | `localStorage` + JSON contract (see DATA-MODEL §6); both read/write lossless | MOCK | Selected provider + `exportDataset()/importDataset()` equivalents (ADR-0003, ADR-0004 pending) |
| 8 | **Geolocation / radius** | Postcode hashed to a deterministic pseudo-co-ordinate in the UK bbox (haversine distance) | MOCK | Real postcode→lat/lng geocoder + radius search (server-side for v1 volumes) |
| 9 | **Images / photos** | Inline SVG placeholders per make/colour | MOCK | Real image CDN + upload pipeline; moderation |
| 10 | **Search backend** | Client-side `Array.filter` over facets | REAL (for scale=700) | Facet-capable search tier (Elasticsearch/Solr/Typesense) when listings > ~10k |
| 11 | **Hosting / delivery** | GitHub Pages from `/docs`, zero build static | REAL | Real CDN (Cloudflare/Azure CDN), app aliases, https |
| 12 | **Analytics** | none (console logging only) | NONE | Privacy-first web analytics (no cross-site) |
| 13 | **State sharing (URL hash)** | Hash encodes filters; restored on load; tests live | REAL | Same URL pattern maintained |

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

---

*Companion docs:* `PRD.md` · `DATA-MODEL.md` · `docs/adr/0001–0003`