# Data Model — Pine It v0.1.0

> Provider-agnostic conceptual schema for the PoC. Every entity is a plain JSON document so any persistence layer (document store, relational, key-value, on-prem → Azure) can hold it without remodelling. **Migration across providers = export this JSON shape, import it somewhere else.** See ADR-0003.

---

## 1. Core entities

```jsonc
{
  "id": "c-0001",                                   // stable, human-readable id
  "source": "gumtree",                              // → Source Registry (section 2)
  "title": "2019 Ford Fiesta ST-Line 1.0T EcoBoost",
  "description": "…free-text seller description…",
  "vehicle": {
    "make": "Ford", "model": "Fiesta",
    "year": 2019, "mileage": 38200,
    "fuel": "Petrol", "gearbox": "Manual",
    "body": "Hatchback", "doors": 5,
    "engineSize": 1.0, "colour": "Grey",
    "condition": "Used"
  },
  "price": { "amount": 8495, "currency": "GBP" },   // amount:cent-aware int, currency: value object (section 3)
  "location": { "city": "Birmingham", "postcode": "B1 2AA", "lat": 52.4862, "lng": -1.8904 },
  "contact": {                       // resolved from SourceRegistry.contactMode
    "mode": "brokered",
    "outboundUrl": "https://www.gumtree.com/p/…?utm_source=pineit&aff_id={AFF}",
    "seller": null
  },
  "status": { "sold": false, "featured": false, "pinnedAt": null, "postedAt": "2026-08-18T11:00:00Z" }
}
```

_(Note: the excerpt above is illustrative; the PoC code in `index.html` is the executable definition of these shapes.)_

## 2. Source registry (extensible — add a platform = one row + seed data)

| source id | label | contact mode | outbound pattern |
|---|---|---|---|
| `pine_it_native` | Native | direct (in-app) | — (no external URL) |
| `gumtree` | Gumtree | `direct` or `brokered` | `https://www.gumtree.com/p/…` + affiliate param |
| `facebook_marketplace` | Facebook Marketplace | `brokered` (no public contact) | `https://www.facebook.com/marketplace/…` + affiliate param |
| `autotrader` | AutoTrader | `direct` | `https://www.autotrader.co.uk/car-details/{id}…` + affiliate param |
| `motors.co.uk` | motors.co.uk | `direct` | `https://www.motors.co.uk/car-{id}…` + affiliate param |

Rules: `mode=native` → in-app contact flow; `mode=direct` → outbound link to source (affiliate-ready URL); `mode=brokered` → "Request via Pine It" lead flow (Pine It forwards, since the source never exposes the seller). All badge colours/CTAs derive from this table.

## 3. Value objects (multi-market-readiness)

- **Price** = `{ amountInteger, currency }`. UI formats via `Intl.NumberFormat(currency)`; no bare numbers in the model.
- **Location** = `{ city, postcode, lat, lng }` + a `distanceKm`/`distanceMiles` computed at query time (never stored); units form configurable.
- **Quantities** such as `engineSize` and `mileage` are declared with their unit (l, miles) alongside — naive numeric comparisons are avoided across markets.

## 4. Facet schema (the "strongest filter engine" contract)

Facets are **declarative and category-owned** (ADR-0002); a category's facet schema is a data array, not UI code:

| facet key | label | type | options / step |
|---|---|---|---|
| `price` | Price | range | £0–£50,000 step 500 |
| `year` | Year | range | 1910–current year step 1 |
| `mileage` | Mileage | range | 0–180,000 miles step 5,000 |
| `make` | Make | multi | all makes (seeded) |
| `fuel` | Fuel | multi | Petrol, Diesel, Hybrid, Electric |
| `gearbox` | Gearbox | multi | Manual, Automatic |
| `body` | Body | multi | Hatchback, Saloon, SUV, Estate, Coupe, Convertible |
| `colour` | Colour | multi | White, Black, Grey, Silver, Blue, Red, Green, Orange, Yellow |
| `condition` | Condition | multi | New, Nearly new, Used, Good condition, For parts |
| `radius` | Distance | radius | 10/25/50/100/250 miles (needs user postcode) |
| `source` | Platform | multi (special) | SourceRegistry ids — "show all / selected / exclude / hide Native" |

Remaining value: later general-marketplace categories declare their *own* schema the same way.

## 5. Seed data strategy (PoC)

- **Deterministic generator** in `index.html`: seeded PRNG (`mulberry32(20260819)`) — same dataset on every load/reviewer.
- **~700 listings**, UK-plausible: 25 make/model families, 1910–current year (modern-heavy mix, incl. classic-car pocket), £500–£48,000, 5–180k miles, fuel/gearbox/body/colour distributions, realistic postcode areas across 15 UK cities.
- **Source mix:** Native 40% · Facebook 20% · Gumtree 18% · AutoTrader 14% · motors.co.uk 8%. Broker mode assigned per rules in section 2 (FB mostly brokered, etc.).
- User-generated rows (posted ads, lead intents) merge with seed data at render time; write to `localStorage` (`pineit_my_ads`, `pineit_leads`).

## 6. Import/export contract (migration seam)

`exportDataset() → JSON` and `importDataset(JSON)` — both lossless, round-trippable. v1's DB decision (ADR-0004, pending) must name a provider that can persist this JSON contract and implement both, after which provider migration = export → import. No provider-specific types may enter the schema in the meantime.

## 7. Open items (tracked, not decisions)

- Real images/photo flow (PoC uses SVG placeholders only — see production-readiness).
- Geolocation truthfulness (PoC hashes the user's postcode to a pseudo-coordinate).
- Affiliate-program specifics (placeholder param names pending actual affiliate contracts).