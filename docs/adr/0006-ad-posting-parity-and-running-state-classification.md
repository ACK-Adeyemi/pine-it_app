# ADR-0006: Ad-posting parity + Spares & Repairs / Non Starter classification

## Status

Accepted (PoC Revision 6)

## Context

PoC Revision 6 raises two problems:

1. **Posting parity.** The post-an-ad form collected only 12 core fields while the filter engine exposes ~24 searchable specification attributes — a native ad could be invisible to half of Pine It's own "strongest filter engine", let alone reach parity with motors.co.uk / AutoTrader advert forms.
2. **Running-state discernment.** Buyers need a reliable way to distinguish running vehicles from **Spares & Repairs** and **Non Starter** stock. The rules file asks whether an LLM is needed or whether a strong phrase list suffices.

Two constraints shape the answer: the PoC must remain a **static single-file app with zero build/backend/API keys** (ADR-0001), and posting must stay quick ("post in under a minute"). A prior stakeholder decision fixed the UX: the expanded fields live in an **optional collapsible section** with deterministic auto-fill for blanks, and users must be able to **explicitly select Spares & Repairs or Non Starter when creating their post**.

## Decision

- **Optional "Specification & running details" section in the post form**, exposing one field per filter-engine facet (plus EV/hybrid extras gated on fuel). `makeAttrs()` gains an overrides argument: any seller-entered value wins; blanks fall through to the existing deterministic derivation, so **every ad remains fully filterable** and the fast-path flow is unchanged.
- **Running status is seller-authoritative.** The form offers *Auto-detect (default) · Running · Spares & Repairs · Non Starter*. An explicit non-running choice is stored directly (`attrs.run`) and is never overridden by text analysis; auto-detect only classifies when the seller leaves it on auto.
- **Classification is a deterministic client-side phrase list** (`runningStatus(title, description, condition)`), NOT an LLM: word-boundary-matched curated phrases with precedence **Non Starter > Spares & Repairs > Running** and `condition==='For parts'` ⇒ repairs. Rationale: offline, free, instant, auditable, deterministic (fits the seeded-dataset ethos) and needs no keys/server — an LLM would contradict ADR-0001 for this PoC. Legacy localStorage ads saved before Rev 6 are classified lazily at render time, keeping old posts correct.
- **Facets stay data-driven (ADR-0002).** A new "Condition & running" section adds `srr` ("Show Spares & Repairs") and `ns` ("Show Non Starters") toggles. Gating lives in `matches()`: the default view suppresses non-runners until their toggle is enabled — mirroring the existing sold-status default-hide pattern — so chips, live counts, collapse-all and URL-hash sharing work with zero special-casing elsewhere. Badges (amber/red) appear on cards and the detail modal, and the detail modal renders the full `attrs` spec sheet.

## Alternatives considered

| # | Alternative | Why rejected |
|---|---|---|
| 1 | LLM-based classification (client or server) | Needs API keys/a backend, breaks the zero-build single-file PoC (ADR-0001); non-deterministic output complicates seeded demos. Kept as a documented v1 enhancement (server-side assist on harvested feeds, provider TBD). |
| 2 | Comprehensive visible form (no collapsible section) | Contradicts the agreed fast-flow UX; risks the "post in under a minute" success metric. |
| 3 | Only string-matching filters without storing `run` (compute in the filter predicate every tick) | Duplicated logic between filter/badge/detail paths and re-scanned every render; stored `attrs.run` (with lazy fallback) keeps one source of truth. |
| 4 | Keyword-only approach (users search "spares") | Not a *filter*: no default hiding of non-runners, no counts, no badge semantics, easily missed. |

## Consequences

- Native ads are first-class citizens of the facet engine — anything filterable is describable at posting time.
- Phrase lists are actively maintained data (top up from observed false negatives/false positives); the `phraseHit` helper escapes dashes/apostrophes and enforces boundaries to avoid e.g. "sport" matching "spores"/"parts".
- Default-hiding non-runners changes the headline result count (≈552 of 705 seeded cars show by default) — intended behaviour, verified, and surfaceable again via the two new toggles.
- Production path (tracked in `PoC-PRODUCTION-READINESS.md`): spec-data from DVLA/vehicle-data partners feeding the same fields; server-side ML/LLM assist to enrich harvested-feed titles/descriptions before `attrs.run` is assigned.

## Links / references

- ADR-0001 (static single-file PoC); ADR-0002 (facets-as-data / source registry); ADR-0004 (filter controls & units)
- `.clinerules/pia-v1-core-rules.md` (§ PoC Revision 6); `docs/DATA-MODEL.md` (attrs schema incl. `run`)
- `docs/PoC-PRODUCTION-READINESS.md` (mock/real inventory rows updated)
