# ADR-0007: Advertising assets and the demo management console (PoC Revision 7)

> **Architecture Decision Record (ADR)** template.
> Pattern based on Michael Nygard's *Documenting Architecture Decisions*.

## Status

Accepted (implemented v0.4.0, 2026-08-27)

## Context

- Core rules PoC Revision 7 asks for **non-obstructive advertising**: an ad banner row repeating every 9 car-ad cards, an embedded sponsored card within the space of a car ad every 10 cards, and a **fixed banner under the nav** whose *content and visibility can be controlled from an external file*.
- It also requires **a straightforward management system** ("ideally an admin UI console") covering banners, **ad moderation and account moderation**, plus a production-readiness note recommending a real implementation shape (e.g. a separate URL location).
- Constraints from prior ADRs: static single-file PoC, no backend/API keys (ADR-0001); provider-agnostic JSON data contract (ADR-0003); no accounts and no login are deliberate PoC scope (PRD §7) — so *who moderates* and *what an "account" is* cannot use real auth.

## Decision

1. **Advertising assets are data, not code — one external file.** `docs/ad-config.json` (served next to `index.html`) fully describes banner content, visibility, tones, intervals and sponsored items. The app ships an identical built-in default (`DEFAULT_AD_CONFIG`) so `file://` previews work untouched; over http(s) the file is fetched at boot (`cache:'no-store'`) and deep-merged over the defaults. A per-device admin edit in `localStorage['pineit_ad_config']` overrides everything until reset — demo-only.
2. **Placements are deterministic and honest.** Within the results grid: a full-width **inline banner row** after every *N*th card (default **9**, from core rules) and an **embedded sponsored card** in a car-ad slot after every *M*th card (default **10**). Every ad slot is labelled (`Ad` / `Advertisement` / `Sponsored`), and paid outbound links carry `rel="noopener sponsored"`. The under-nav **fixed banner** renders only when enabled and non-empty.
3. **Sponsored content has three modes** (`sponsored.mode`): `paid` (third-party items), `default` (the slot is **replaced by Pine It's own feature promos** — post-an-ad, the filter engine, one-feed-every-platform — per stakeholder request), and `off` (slot hidden entirely). If `paid` is selected but the paid list is empty/absent, the honest `default` content renders instead — a paid slot is never silently blank.
4. **A demo-grade admin console** (header **Admin** button, **no login** — per stakeholder decision) with three tabs:
   - *Banners & sponsored*: edits nav banner, inline banner row and sponsored mode/interval with instant preview; **Apply & save** (device override), **Download ad-config.json** (produces the exact external file to deploy for all visitors), **Reset to defaults**.
   - *Ads moderation*: lists device-stored posted ads; **Hide/Restore** (render-time filter — nothing is destroyed, counts and facets stay honest because the filter engine reads a `visibleListings()` view) and **Delete** (permanent).
   - *Accounts moderation*: with no login, an "account" is **a person who interacted on this device** — ad posters (identified by the optional display name added to the post form) and lead requesters (name+email from the lead flow). **Block/Unblock** suppresses all their posted ads at render time. Production replaces these device-local keys with real user IDs and server-side flags.

## Alternatives considered

| # | Alternative | Why rejected / deferred |
|---|---|---|
| 1 | Real ad server / ad-manager SDK (GAM, AdSense) | Contradicts ADR-0001 (no external scripts, no API keys) and the mock guardrails; recorded as the production path. |
| 2 | Auth-protected admin console now | No account system exists (PRD §7); stakeholder chose the open demo-grade console. |
| 3 | Hard-code banners in `index.html` | Fails the core-rules requirement that nav-banner content/visibility be externally controllable without code edits. |
| 4 | `fetch` the config unconditionally | Breaks `file://` previews (fetch/CORS); the built-in default fallback keeps zero-build behaviour (ADR-0001). |
| 5 | Permanent account/email fields on the post form | Only a light optional display name is added, purely so the demo moderation console can group posted ads into "accounts". |

## Consequences

- **Positive:** marketing/ad-ops-style changes need only a JSON edit; the whole ad surface can be toggled off at once; moderation never destroys data; every placement stays labelled and honest; the admin demo shows the intended production workflow end-to-end.
- **Negative:** the console is unauthenticated (anyone can open it) and its state is per-device only — it must never be presented as real; intervals/rotations are client-side and therefore user-manipulable in the PoC.
- **Neutral:** the external `ad-config.json` becomes part of the deployment surface (must be committed/uploaded next to `index.html`); the ad-config and moderation stores join the provider-agnostic JSON contract (DATA-MODEL §6) so the v1 database decision (ADR-0004) can absorb them unchanged.

## Links / references

- `docs/ad-config.json` (external asset config) · `docs/index.html` → `DEFAULT_AD_CONFIG`, `effAdConfig()`, `loadAdConfig()`, `renderAdBanner()`, `bannerRowHtml()`, `sponsoredCardHtml()`, `renderResults()`, admin console (`openAdmin()`, `renderAdminBanners()`, `renderAdminAds()`, `renderAdminAccounts()`), moderation store (`pineit_moderation`), `visibleListings()`.
- ADR-0001 (static PoC), ADR-0002 (facets-as-data / extensible registry), ADR-0003 (provider-agnostic data), `docs/PoC-PRODUCTION-READINESS.md` (production ads path: separate auth-protected console URL + ad server/CMS + DB-backed moderation).
- Core rules: `.clinerules/pia-v1-core-rules.md` → PoC Revision 7.
