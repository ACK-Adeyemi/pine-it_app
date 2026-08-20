# Changelog

All notable changes to this project are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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