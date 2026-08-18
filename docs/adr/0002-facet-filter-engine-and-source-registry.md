# ADR-0002: Facet filter engine & extensible source registry

## Status

Accepted

## Context

The product's core differentiator is the **strongest filter/search engine on the market**. The rules and planning sessions fixed four hard requirements:

1. **Combined power** — full-text search must compose with rich facet filtering (ranges, multi-select) and update instantly.
2. **Platform is a first-class facet** — the mixed feed must let users *show all sources, show selected sources, exclude specific platforms, and even hide Native* listings.
3. **Sources must extend cheaply** — today: Native, Gumtree, Facebook Marketplace, AutoTrader, motors.co.uk. Future car-selling platforms must be addable **without touching filter logic**.
4. **General marketplace later** — the roadmap includes non-car categories ("sell anything"), so facets must be *category-declared/data-driven* rather than hard-coded form fields.

## Decision

Build a **facets-as-data engine** over two declarations:

### 1. Facet schema (declared per domain — currently `vehicles/cars`)
A facet is a declarative object: `{ key, label, type, options|min|max|step }` where `type ∈ { multi, range, radius }`. Filtering is a pure intersection pass over active facets (`matches(listing, activeFilters)`). Per-facet option counts are computed against all active filters *except the facet being counted* (standard faceted-count semantics). Range facets render as min/max controls; multi-select facets render checkbox groups with counts.

### 2. Source registry (one extensible data structure)
```js
{ id: 'gumtree',  label: 'Gumtree',  urlPattern: '…?utm_source=pineit&aff_id={AFF}',
  badgeColour: '#f59e2a', contactMode: 'direct',   broker: false }
{ id: 'facebook', label: 'Facebook Marketplace', … contactMode: 'brokered', broker: true }
{ id: 'native',   label: 'Native', contactMode: 'native' }
```
Every card, badge, outbound link builder, lead/routing decision and the `Source` facet read **only** from this registry. Adding `ebay-motors` or `cazoo` is one registry entry + seed data — zero engine changes.

Additionally, active filter state is serialised to the **URL hash** (e.g. `#q=…&make=ford,bmw&source=gumtree,autotrader&radius=50`) and applies on load, making result views shareable.

## Alternatives considered

| # | Alternative | Why rejected / deferred |
|---|---|---|
| 1 | Hand-coded filter forms per platform/category | Duplicates every facet in markup; every "show only", "hide Native" interaction needs bespoke wiring per source; fails the extension rule. |
| 2 | Client-side search library (e.g. Lunr, FlexSearch) | Adds dependency/build complexity for a PoC whose dataset (≈700–5k) sorts trivially with `Array.filter`. Revisit for v1 volumes. |
| 3 | Server-side Elasticsearch/Solr | No backend exists yet (ADR-0001); design intent is preserved by the same facet model for a later search tier (documented in DATA-MODEL). |

## Consequences

**Positive:** adding a platform or a facet is a *data* change; UI, sort, counts and sharing stay identical by construction; the same engine later serves general categories ("sell anything") by adding new facet schemas.

**Negative:** per-facet-count recomputation is O(options × listings) — fine at PoC scale, must be re-measured for production volume (search-tier ADR needed then).

**Neutral:** facet definitions effectively become part of the product's API surface; they belong version-stamped in DATA-MODEL.md alongside the listing schema.

## Links / references

- `docs/DATA-MODEL.md` (facet schema + source registry definitions)
- ADR-0001, ADR-0003; `docs/PRD.md` (filter-engine capability checklist)