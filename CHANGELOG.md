# Changelog

All notable changes to this project are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.3.0] — 2026-08-26

### Added

- **PoC Revision 6** (per `.clinerules/pia-v1-core-rules.md`):
  - **Expanded "Post an ad" form** — a new optional, collapsible **"Specification & running details"** section brings posting to parity with motors.co.uk/AutoTrader by exposing every field the filter engine can search on: engine size (L), seats, doors, power (bhp), drivetrain, annual tax, insurance group, fuel economy (MPG), CO₂, ULEZ, number of keys, safety rating, has-MOT/imported flags, damage category (None/Cat N/S/C/D), popular features (CarPlay, Bluetooth, Leather seats, Wheelchair access), towing weights (braked/unbraked), seller/dealer type and part-exchange availability. **EV & hybrid extras** (range, fast-charge time, leased battery, battery warranty) appear only when fuel is Electric/Hybrid. Blank fields are auto-filled by deterministic defaults (`makeAttrs(rng,body,fuel,year,overrides)`), so **every posted ad stays fully filterable**, and sellers can pick doors/engine size without opening the section.
  - **Explicit Running-status selector in the post form** — sellers choose Auto-detect *(default)*, **Running**, **Spares & Repairs** or **Non Starter**. An explicit **Spares & Repairs** or **Non Starter** selection always wins over automatic wording detection; auto-detect remains as a backstop (e.g. someone who forgets the selector but writes "breaking for spares").
  - **Spares & Repairs / Non Starter filters** — a new **"Condition & running"** facet section (per ADR-0002 facets-as-data) with two toggles that read each listing's classified state: the default feed now shows **running vehicles only**; ticking *Show Spares & Repairs* / *Show Non Starters* surfaces both explicitly-selected and auto-detected listings. Chips ✕/Clear-all, live counts, collapse-all and URL-hash sharing all work through the existing engine paths.
  - **Deterministic phrase-list classifier** (`runningStatus(title,description,condition)`) — word-boundary-matched strong phrase lists with precedence **Non Starter > Spares & Repairs > Running**, plus `condition==='For parts'` mapping to repairs. Chosen over an LLM because the PoC is a static single-file app with no backend/API keys (ADR-0001); the LLM path is documented as a production enhancement in **ADR-0006**. Legacy `localStorage` ads saved pre-Rev 6 are classified on the fly, so old posts stay correct.
  - **Seeded demo pocket** — deterministic slice of the catalogue (~144 "For parts"-condition cars re-titled/priced as spares, ~10 explicit non-runners repriced at scrap-level values) so the new filters demo non-empty without breaking reproducibility (705 total unchanged).
  - **Badges & full spec sheet** — cards and the detail view now show a **Spares & Repairs** (amber) / **Non Starter** (red) badge, and the detail view renders the complete `attrs` spec sheet (`attrRows`) — what you can filter on is exactly what you see.
  - New **ADR-0006** (`docs/adr/0006-ad-posting-parity-and-running-state-classification.md`).

## [0.2.4] — 2026-08-26

### Fixed

- **PoC Revision 5 remediation** (user-reported bugs in v0.2.3):
  - **NaN / undefined in showcase meta** — `renderShowcase()` passed the listing object where `scMeta()` expects a `vehicle`; now passes `l.vehicle`. `fmtNum` hardened (never renders "NaN"/"undefined"; returns "—" for missing values) and `scMeta` skips empty parts.
  - **Links opened listing/search pages instead of advert (PDP) pages** — all competitor deep-links replaced with verified live single-ad URLs captured at build time: motors.co.uk `car-79576221` (2003 Corsa Club, £1,295), AutoTrader `car-details/202606012893471` (2010 Corsa SE, £1,995, BM Car Sales Bedford), Gumtree `p/ford/2019-ford-fiesta-1.0-ecoboost-140-st-line…/1802012169` (verified via its structured data; replaces the expired ID.4 ad). Card facts updated to match each real ad exactly.
  - **Motors.co.uk link opened Cazoo** — caused by linking a category URL that redirects into the Cazoo-branded estate; the real motors.co.uk advert page keeps the browser on motors.co.uk (page branding may say Cazoo — same company since Jun 2024; documented in ADR-0005).
  - Showcase gained a generic `linkNote`; Facebook stays area-level with its login-gate note. **Cazoo upgraded (same day):** product owner supplied a real single-car PDP (`cars-for-sale/79613297`) plus its full figures — card now shows the verified 2017 Ford Fiesta 1.0 EcoBoost 140 ST-Line 5dr, £4,595, 75,000 mi, from Ghost Motors Ltd (Cleckheaton); owner-transcribed because Cazoo's bot-wall blocks machine reading.
  - **Facebook Marketplace** remains an area-level link with an explicit "Facebook login required" note on-card (items are account-walled).

## [0.2.3] — 2026-08-25

### Added

- **PoC Revision 5** (per `.clinerules/pia-v1-core-rules.md`):
  - **Live competitor showcase** — a "Live examples from our competitors" strip above the results with one real snapshot per platform in the Core Competition order (**motors.co.uk, AutoTrader, Cazoo, Gumtree, Facebook Marketplace**). Each card carries real factual listing data, a real deep-link to the platform, and is labelled **snapshot** + **Representative image**; the examples also flow through the facet engine, platform filter, chips and detail view alongside the 700 seeded listings.
  - **Cazoo is now a first-class source** — new `cazoo` entry in the source registry, so its badge, colour, platform facet and CTA derive automatically (ADR-0002); documented as a trading name of Motors.co.uk Limited.
  - **Representative-image policy (ADR-0005)** — no competitor photograph is stored or hotlinked. Every card renders Pine It's own car illustration, labelled **"Representative image"** (Trader v CarGurus rationale + per-platform ToS review recorded in ADR-0005). Zero licence cost, zero ToS exposure, robust offline.
  - **Facebook Marketplace handling** — individual items are behind a Facebook login, so the example is a clearly-labelled representative Marketplace card with a Marketplace-area deep link and an inline "Facebook login required" note.
  - **Mobile app routing** — `openCompetitor()` uses each source's registered `appScheme` on touch devices for "View on [platform]", falling back to the https URL (Universal/App Link → installed app else default browser); desktop opens a new tab.
  - New **ADR-0005** (`docs/adr/0005-real-example-showcase-and-representative-images.md`).

### Changed

- `docs/index.html` seeded catalogue is now **705 listings** (700 deterministic + 5 pinned real examples).

## [0.2.2] — 2026-08-24

### Added

- **PoC Revision 4** (per `.clinerules/pia-v1-core-rules.md`):
  - **Collapse all / Expand all** — new buttons in the filter-pane header collapse or expand every filter group at once (`setCollapsedAll`, also covering location/platform/ranges/multi-selects/sectioned groups), independent of the search auto-collapse behaviour.
  - **Generic, direction-aware sorting** — the Sort dropdown now holds **Featured · Price · Year · Mileage · Distance**; choosing a field defaults to a sensible direction (Year desc, the rest asc) and a toggleable **arrow button** (`▲`/`▼`) next to the dropdown flips asc ↔ desc and re-sorts on click. `compare()`/`packageState()` updated; direction is not offered for Featured.
  - **Search your filters** — the left pane is renamed **"Filter Options"** and gains a fixed (non-scrolling) **search box** beneath the header with a one-tap **✕** clear. `renderFacets()` now filters groups/options to live **highlighted** matches (matching groups force-open), and the location-radius group participates too. Filter search + sort direction persist to the URL hash (`applyPayload`/`packageState`).

- **PoC Revision 3** follow-ups validated alongside (per-chip ✕ and Year chip formatting) — unaffected.

## [0.2.1] — 2026-08-24

### Fixed

- **PoC Revision 3** (per `.clinerules/pia-v1-core-rules.md`):
  - **Per-chip ✕ removal now works** — the chip-remove delegate read the filter type from `d.type`, but the button declares it as `data-chip` (i.e. `dataset.chip`), so `d.type` was undefined and no `removeChip` branch matched. It now reads `d.chip`. Range, set (multi-select), keyword and radius chips each clear independently; **Clear all** is unchanged.
  - **Year chip no longer shows thousands separators** — a range label like `2001 – 2003` was rendered as `2,001 – 2,003` because `boundLabel()` ran the year through `fmtNum()` (`toLocaleString('en-GB')`). `boundLabel` now formats year bounds as plain integers.

## [0.2.0] — 2026-08-19

### Added

- **PoC Revision 2** (per `.clinerules/pia-v1-core-rules.md`):
  - **Smart multi-keyword search** — the search bar now splits on commas into separate, removable **keyword chips** (e.g. `Qashqai, petrol`); results match **all** terms (AND). Rendered per-term in the active-filter chip row.
  - **Missing filters added** — **Engine size (L)** (range) and **Doors** (multi-select), plus the keyword search text now indexes engine/door data.
  - **Full motor.co.uk-style filter set** — ~24 new filters covering **Running costs** (annual tax, insurance group, fuel economy, CO₂), **Performance** (power/BHP, drivetrain, towing braked/unbraked), **EV & hybrid** (range, fast-charge, leased battery, battery warranty), **Features** (CarPlay, Bluetooth, Leather, Wheelchair), **Safety & condition** (safety rating, keys, MOT, ULEZ, seats), **Vehicle history** (imported, exclude Cat N/S/C/D), **Vehicle usage** (exclude ex-hire/fleet, ex-demo), **Dealer** (rating, type, part-exchange) and **Advert options** (sold status, 2+ images, reduced, recently added) — rendered under motors.co.uk-style grouped parent sections.
  - **Per-field filter controls + units** decided in **ADR-0004** (raw min/max inputs only where the value & unit are unambiguous; tick-box buckets elsewhere; multi/toggle for categorical/state fields), replacing the earlier all-range approach.
  - Seed listing model extended with deterministic `attrs` for every new field (correlated with fuel/body/age); filter engine (`attrMatch`) handles buckets, multi, array-features and flag semantics.

## [0.1.1] — 2026-08-19

### Added

- **PoC Revision 1** (per `.clinerules/pia-v1-core-rules.md`):
  - **Year/Age filter widened to 1910 → current year** (dynamic) in the facet panel and the "Post an ad" form.
  - **Widened seed dataset** — modern-heavy year mix from 1910 (classic-car pocket) to the current year, with age-safe price/mileage generation.
  - **Active-filter chip row** above the results — every filter (including the search text) is a removable chip (**✕**) with a working **Clear all** button.
  - **Motors.co.uk-style left filter pane** — all filter groups (Platform, Location-radius, ranges, multi-select) are collapsible accordions with active-count badges; groups **auto-collapse while a search is active** and re-expand when cleared.
  - **Cazoo-aligned mobile layout** — filter panel becomes a bottom sheet with a "Show N results" apply bar, compact 2-column result cards, a horizontally scrollable chip row, and an active-filter count badge on the filter toggle.

## [0.1.0] — 2026-08-19

### Added

- **PoC documentation set** (prerequisite to build, per core rules):
  - `docs/PRD.md` — vision, PoC scope, "strongest filter engine" capability checklist, acceptance criteria, 3-minute demo script.
  - `docs/DATA-MODEL.md` — provider-agnostic data model, extensible source registry (Native, Gumtree, Facebook Marketplace, AutoTrader, motors.co.uk), facet schema, seed strategy.
  - `docs/PoC-PRODUCTION-READINESS.md` — full inventory of real vs mocked integrations with planned real paths.
  - `docs/adr/0001-static-single-file-poc-architecture.md`, `docs/adr/0002-facet-filter-engine-and-source-registry.md`, `docs/adr/0003-provider-agnostic-data-strategy.md`, plus `docs/adr/template.md`.
- **Single-file PoC app** (`docs/index.html`): vanilla HTML/CSS/JS marketplace, zero build, GitHub-Pages-ready.
  - 700+ seeded UK car listings across 5 sources, deterministic generation.
  - Facet filter engine (full-text + ranges + multi-select facets + source/platform facet + postcode distance radius + live per-facet counts + sorting), selected filters synced to the URL hash.
  - Listing cards with platform badges and per-listing CTA (outbound affiliate-ready link or **Request via Pine It** lead flow).
  - "Post an ad" form persisted to `localStorage`, appears live as **Native**; "My activity" panel lists posted ads and lead requests; demo reset.
  - Pine-green, modern marketplace UI, responsive with mobile filter panel.
- **Root-level `README.md`** with local-run and GitHub Pages deployment instructions.
- Git repository initialised (`main` branch); `.gitignore` for OS/editor clutter.

### Notes for reviewers

- GitHub Pages hosting is configured out-of-band (Settings → Pages → Deploy from `main /docs`); no CI workflow committed yet.
- Platform badge hues are approximations; confirm brand usage and affiliate-program terms before production.