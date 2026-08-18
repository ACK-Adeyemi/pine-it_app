# Product Requirements — Pine It v0.1.0 (PoC)

> **One sentence:** Pine It is a UK car marketplace where one search shows every car from every platform — with the strongest filter engine on the market, dirt-cheap zero-fee selling, and attribution-first harvesting of Gumtree, Facebook Marketplace, AutoTrader and motors.co.uk.

- **Type:** PoC product requirements & acceptance criteria
- **Version:** v0.1.0 — 2026-08-19
- **Build target:** single-file static web app (`docs/index.html`) — see ADR-0001

---

## 1. Vision & the gaps we exploit

| Today's option | The gap we capitalise on |
|---|---|
| Facebook Marketplace | Requires a Facebook account (account wall) |
| Gumtree | Terrible/limited filters; cluttered UI; stale listings |
| motors.co.uk | Not an app; flat search |
| AutoTrader | Expensive for private sellers |

**Pine It's answer:** a **cars-first general marketplace** with an open-by-default, no-account-wall browse-and-contact flow, polish that makes Gumtree look dated, a **facet filter engine stronger than anything in the category**, and — uniquely — a **single mixed feed of native + harvested listings** presented with source badges and privacy-safe contact routing. Data, currency, and location stay provider/market-agnostic by design (ADR-0003), ready to spread across Europe next.

---

## 2. PoC scope (decided)

| Item | Decision | Source |
|---|---|---|
| Feature slice | **Full vertical slice**: browse → search/filter → open listing → contact (outbound/lead) → post an ad → see it live | Q1 |
| Vertical | **Cars-first** automotive marketplace; general items = future extension via the same facet engine | Q2 |
| Harvested ads | **Mixed into the main feed** with **"via [platform]"** badges + affiliate-ready outbound links | Q3 |
| Platform facet | Show all · show selected · **exclude platforms** · **hide Native** | Q3 |
| Commercial model | **Hybrid**: affiliate-ready referral links *and* Pine It as lead/broker where sources don't expose seller contact | Q4 |
| Geo/market | UK demo data; **multi-market-ready** (currency/location/units configurable) | Q5 |
| Brand | Pine-green, clean, modern marketplace look | Q6 |

---

## 3. People & jobs to be done

1. **Private car sellers** — "I want to sell my car easily for cheap, across every platform, without fees or accounts."
2. **Budget/browsing buyers** — "I want every car on sale anywhere in one search, then cut straight to what I can afford near me."
3. **Cross-platform window-shoppers** — "Gumtree/FB/AutoTrader/motors.co.uk each have half the market; someone should merge them and let me filter properly."

---

## 4. Hero capability: the "strongest filter engine" (definition of done)

The PoC must ship *all* of the following — this is the acceptance bar:

- [x] **Combined** full-text search + facet filtering (one query applies to the whole collection)
- [x] **Multi-select facets** (checkbox groups: make, fuel, gearbox, body, colour, condition, platform…)
- [x] **Range facets** (price, year, mileage via min/max inputs)
- [x] **Postcode distance radius** (10/25/50/100/250 miles, distance listed + sortable)
- [x] **Live per-facet result counts** (each checked option shows exactly the count it yields)
- [x] **Instant re-render** on any change (no submit, no reload)
- [x] **Platform facet** with show-all / selected-only / exclude / **hide Native** semantics
- [x] **Sorting**: featured, price ↑↓, year, mileage, distance
- [x] **Clear all** + empty-state reset
- [x] **URL-hash state** so any filtered view is shareable/bookmarkable

## 5. Full acceptance criteria (buyer → seller vertical)

1. From a cold load, a visitor can go from ~700 mixed listings to a precise, sorted, radius-limited set in **≤ 5 clicks**.
2. Every harvested card shows its **source badge** and performs its correct CTA (affiliate outbound link in a new tab, or **Request via Pine It**).
3. The **platform facet** demonstrably supports show-all, selected-only, exclude-a-platform, and hide-Native.
4. **Post an ad** persists; the new listing appears instantly with a **Native** badge and survives a refresh.
5. **Lead flow** ("Request via Pine It") completes with a confirmation, persists, and shows under "My activity".
6. Filters survive reload via URL hash; sharing a URL restores the exact view.
7. UI is responsive (filters accessible on mobile) and keyboard-navigable modals close on `Esc`.

## 6. Demo script (~3 minutes) — the hero moment

1. **0:00** Land. Show the one feed: a wall of cars wearing different platform badges. *"Every car on the internet, one room."*
2. **0:30** Type "Qashqai" — the feed narrows instantly; add "petrol".
3. **0:50** Tick *Petrol*, *Automatic*, price ≤ £20k, mileage ≤ 40k; watch per-facet counts drop live.
4. **1:20** Enter a postcode + 50-mile radius; distance labels and distance sorting appear.
5. **1:45** **Hide Native** — now showing *only* harvested cars; uncheck to restore. Point at a "via Facebook Marketplace" badge + **Request via Pine It** CTA vs AutoTrader's outbound affiliate link.
6. **2:20** Post an ad in 60 seconds; it lands at the top with a **Native** badge.
7. **2:50** Copy the filtered URL and paste it — state restored. *"Now we go sell this."*

## 7. Out of scope (v0.1.0)

- Accounts, login, identity verification, payments
- Real scraping/API ingestion for imported platforms (mock only)
- Real affiliate payouts, lead forwarding/CRM (stub only — see `PoC-PRODUCTION-READINESS.md`)
- Real images/photos pipeline, messaging, chat, saved searches, push
- Non-facet-engine categories (ready but un-shipped): see ADR-0002

## 8. Success metrics to observe during live testing

- Do testers immediately **understand the platform badge + broker/direct distinction**? (qualitative)
- Filter usage: % of sessions where filters are used (visible via shared hash URLs)
- Post-an-ad completion rate (submit / session)
- Time-to-first-filter appetite vs time-on-page

## 9. Risks

| Risk | Mitigation |
|---|---|
| Harvest-sourcing legality/ToS | PoC uses mock data only; production requires API/partnerships or compliant scraping + take-down (see production-readiness) |
| "Mock data feels fake" | Deterministic realistic UK dataset; native flow genuinely persists in localStorage |
| Scope creep (categories, accounts) | Explicit out-of-scope list; acceptance gate kept on the engine hero moment |

---

*Companion docs:* `DATA-MODEL.md` · `PoC-PRODUCTION-READINESS.md` · `docs/adr/0001–0003`