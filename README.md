# Pine It — v0.2.0 PoC (Revision 2)

> **Pine It** — *"sticky notes for the car market."* A marketplace with the strongest filter engine on the market: one search, every car, every platform.

Pine It is a UK-focused, cars-first marketplace exploring a better alternative to Gumtree, Facebook Marketplace, AutoTrader and motors.co.uk:

- **No account wall** — no Facebook/Google login required to browse or contact (unlike Facebook Marketplace).
- **Best-in-class search & filters** — a facet-based filter engine that out-filters Gumtree and out-searches everything else.
- **Cheap, low-barrier selling** — post an ad in under a minute, no listing fees (unlike AutoTrader).
- **One feed, every platform** — harvests car ads from other platforms (Gumtree, Facebook Marketplace, AutoTrader, motors.co.uk) and displays them mixed into a single searchable feed with clear source attribution, affiliate-ready outbound links, and a lead/broker path where sellers aren't directly reachable.

This repository contains the **v0.2.0 proof-of-concept (PoC Revision 2)**: a single-file static web app (`docs/index.html`) written in vanilla HTML/CSS/JS, designed for zero-build, zero-cost deployment on GitHub Pages. The long-term product is a Flutter app (see `docs/PRD.md` and the ADRs); this PoC validates the concept first.

---

## Live demo

Configure GitHub Pages per the [Deploy guide](#deploy-to-github-pages), or open `/docs/index.html` locally. Once deployed the app is served at:

```
https://<your-user>.github.io/<your-repo>/
```

## PoC feature set

- **Browse** — 700+ seeded, UK-plausible car listings mixed from 5 sources: Native, Gumtree, Facebook Marketplace, AutoTrader, motors.co.uk.
- **The filter engine (hero feature)** — full-text search **combined** with faceted filtering: price/year/mileage/engine-size ranges, make, fuel, gearbox, body, colour, condition, **doors**, multi-select everywhere, live result counts per facet, postcode **distance radius**, and instant re-render.
- **Full motor.co.uk-style filter set** — ~24 extra filters grouped under **Running costs · Performance · EV & hybrid · Features · Safety & condition · Vehicle history · Vehicle usage · Dealer · Advert options**: annual tax, insurance group, fuel economy, CO₂, power/BHP, drivetrain, towing (braked/unbraked), EV range, fast-charge, leased battery, battery warranty, CarPlay/Bluetooth/Leather/Wheelchair, safety rating, keys, MOT, ULEZ, seats, imported, damage category (individually: Cat N/S/C/D + No damage), vehicle usage exclusions, dealer rating/type, part-exchange, sold status, 2+ images, reduced and recently added.
- **Sensible filter controls** — raw inputs only where the value+unit are clear (price/mileage/year); tick-box buckets and multi/toggles everywhere else, with units pinned in labels — see **ADR-0004**.
- **Smart keyword search** — type multiple keywords and separate them with a **comma** to create removable keyword chips (e.g. `Qashqai, petrol`); listings must match **all** terms.
- **Motors.co.uk-style filter UX** — collapsible facet groups (multi-select everywhere); groups **auto-collapse while you search** and re-expand when cleared; each header shows how many options are active.
- **Active-filter chip row** — every active filter (including your search text) sits above the results as a removable chip (**✕**), plus a one-tap **Clear all**.
- **Cazoo-aligned mobile layout** — the filter panel slides up as a bottom sheet with a **"Show N results"** apply bar; results render as a compact 2-column grid with a horizontally scrollable chip row.
- **Platform as a filter** — show all sources, show only selected, exclude specific platforms, or **hide Native** to see purely harvested listings. Every platform is a first-class facet backed by an extensible source registry.
- **Attribution-first harvesting** — harvested cards show a "via [platform]" badge and open an affiliate-ready outbound link in a new tab; where the seller isn't directly reachable the card offers **"Request via Pine It"** (broker/lead flow).
- **Post an ad** — a full car-ad form persisted to browser `localStorage`; your ad appears live in the feed as **Native** and survives reloads.
- **Listing details** — full spec sheet, description and per-listing action (outbound link or Pine It lead).
- **State preservation** — filters persist to the URL hash, so a results view can be bookmarked/shared; refresh keeps your filters.

## Run locally

The app is fully static — **no build step, no dependencies**.

```bash
# Option A: just double-click / open
open docs/index.html            # macOS
start docs/index.html           # Windows

# Option B: serve locally (recommended for testing)
python -m http.server 8000 --directory docs     # Python
npx serve docs                                   # Node
```

Then visit `http://localhost:8000`.

## Deploy to GitHub Pages

1. Create a repository on GitHub and push this project to it:

   ```bash
   git remote add origin https://github.com/<your-user>/<your-repo>.git
   git push -u origin main
   ```

2. In the repo on GitHub: **Settings → Pages → Build and deployment → Source: "Deploy from a branch" → Branch: `main` → folder: `/docs` → Save**.
3. GitHub builds the Pages site from `/docs` (the PoC lives at `docs/index.html`). Your app is live within a minute at:

   ```
   https://<your-user>.github.io/<your-repo>/
   ```

> **Why `/docs`?** GitHub Pages serves the `/docs` folder as the site root with zero build. The PoC uses **only relative links and classic `<script>` tags** — no modules, no `fetch` — so it also runs from `file://` and under the sub-path GitHub Pages uses for project sites.

## Project layout

```
pine-it_app/
├── README.md                ← this file
├── CHANGELOG.md             ← version history
├── docs/                    ← GitHub Pages content root
│   ├── index.html           ← THE PoC (single-file HTML/CSS/JS app)
│   ├── PRD.md               ← product requirements + acceptance criteria
│   ├── DATA-MODEL.md        ← provider-agnostic schema, source registry, facets
│   ├── PoC-PRODUCTION-READINESS.md  ← real vs mock integration inventory
│   └── adr/                 ← architecture decision records
│       ├── template.md
│       ├── 0001-static-single-file-poc-architecture.md
│       ├── 0002-facet-filter-engine-and-source-registry.md
│       └── 0003-provider-agnostic-data-strategy.md
├── .clinerules/             ← agent governance (not published)
```

## More documentation

| Doc | Purpose |
|---|---|
| `docs/PRD.md` | Product vision, PoC scope, "strongest filter engine" capability checklist, acceptance criteria, demo script |
| `docs/DATA-MODEL.md` | Provider-agnostic data model, source registry, facet schema, seed data strategy |
| `docs/PoC-PRODUCTION-READINESS.md` | Which integrations are real vs mocked in the PoC, and the planned real path for each |
| `docs/adr/*` | Architecture decisions (static PoC, facet engine/source registry, provider-agnostic data strategy) |

## Status

**v0.2.0 (PoC · Revision 2)** — see [`CHANGELOG.md`](CHANGELOG.md).