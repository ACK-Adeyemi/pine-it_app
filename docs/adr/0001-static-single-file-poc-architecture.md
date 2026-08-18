# ADR-0001: Static, single-file PoC architecture

## Status

Accepted

## Context

- The core rules set the production ambition as a Flutter marketplace app ("a production-ready better version of Gumtree"), but explicitly gate the build behind documentation and require an initial **HTML/CSS/JavaScript PoC maintained in a single `index.html`**.
- The PoC must validate the highest-risk product assumptions cheaply and fast:
  1. *A filter/search engine that genuinely feels like the strongest on the market.*
  2. *A single mixed feed of native + harvested listings* (one search, every car, every platform) with clear source attribution.
  3. *A frictionless post-an-ad + contact/broker loop* that survives without an account wall.
- The PoC needs a live, shareable location for testing with zero infrastructure cost and zero ops — GitHub Pages was chosen.
- Build must be authorable, reviewable and maintainable by Cline agents with vanilla tooling only.

## Decision

The PoC is a **single self-contained static `index.html`** (vanilla HTML + CSS + JavaScript) — **no framework, no module system, no build step, no server-side state**.

- All user-generated state (posted ads, lead requests) is kept in **`localStorage`**; seed/catalogue data is **generated deterministically in code** (seeded PRNG) so every load (and every reviewer) sees the same dataset.
- The site lives at `docs/index.html` and GitHub Pages serves the `/docs` folder as its root ("Deploy from `main` / `/docs`"). All internal asset references are **relative** and all JS is loaded as **classic `<script>`** (no `fetch`, no ES modules) so the file also runs from `file://`.
- Data for harvested platforms is **mocked in the seed set**; the production surface for each is tracked in `PoC-PRODUCTION-READINESS.md`.
- Active filters are mirrored to the **URL hash** so results views are shareable/bookmarkable with zero server involvement.

## Alternatives considered

| # | Alternative | Why rejected / deferred |
|---|---|---|
| 1 | Framework SPA (React/Vue/Svelte) | Adds build tooling, dependencies and a more complex review surface; the single-file constraint and zero-build GitHub-Pages deploy make vanilla JS strictly simpler for a PoC of this size. Revisit for v1 (Flutter is the production target anyway). |
| 2 | Multi-file static app (`app.js`, `styles.css`, `seed-data.js` split) | Acceptable fallback if `index.html` grows too large to maintain; still fully GH-Pages-compatible. Decision in ADR-0001 favours one file for the PoC *now*; a seed-data split is a documented escape hatch, not currently used. |
| 3 | Server-backed API (auth, DB, scraping pipeline) | Far too heavy for concept validation; contradicts zero-ops live testing. Defers every backend concern to v1 design (see ADR-0003). |
| 4 | Flutter PoC compiled to web | The core rules gate the PoC to `index.html` first; Flutter remains the production path. |

## Consequences

- **Positive:** instant deployment (branch push + Pages setting = live site); works offline / on `file://`; trivially auditable single file; no dependency maintenance; deterministic, reviewable data.
- **Negative:** no real authentication, no cross-tier coordination, no authoritative persistence — these capabilities are intentionally absent and *must* remain documented (see PoC-PRODUCTION-READINESS.md) so they are not mistaken for production features.
- **Neutral:** the eventual Flutter app will not reuse this code; the PoC's value is validating UX/product assumptions and the data model (ADR-0003, DATA-MODEL.md), not its code.

## Links / references

- Core rules: `.clinerules/pia-v1-core-rules.md` (§ "For PoC").
- Adjacent decisions: ADR-0002 (facet engine & source registry), ADR-0003 (provider-agnostic data strategy).
- `docs/PRD.md`, `docs/PoC-PRODUCTION-READINESS.md`, `docs/index.html`.