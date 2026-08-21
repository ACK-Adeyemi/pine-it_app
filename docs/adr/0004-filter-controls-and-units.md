# ADR-0005: Filter control types & units (per-field)

## Status

Accepted

## Context

PoC Revision 2 adds the full motor.co.uk-style filter set (running costs, performance, EV & hybrid, features, safety & condition, vehicle history, vehicle usage, dealer, and "Advert options" grouping). A naive approach — a numeric **min/max input** for every numeric field — fails two real usability tests:

1. **"It may not be obvious what number to enter."** A buyer does not instinctively know the annual tax, insurance group, towing weight, BHP or CO₂ value they want; raw inputs force guesswork.
2. **"Which metric system is the user typing with?"** Same quantities are expressed differently across markets (MPG vs. L/100km, miles vs. km of EV range, £ vs. € tax bands). An unlabelled number input is ambiguous.

A static PoC has no first-party live-user traffic yet, so decisions here follow established UK-marketplace patterns (motors.co.uk, AutoTrader) + a unit-obviousness rule, are **declaratively swappable** (see Consequences), and carry a live-testing milestone.

## Decision

Apply one of three control types per field, chosen by "can the typical buyer name the value *and* the unit off the top of their head?":

- **raw `min/max` input** — only where yes (natural mental model + unambiguous unit);
- **tick-box buckets** — where a number is not easily known/typed, with the unit pinned in the label and bucket text;
- **multi/toggle** — where the field is categorical or a documented state.

| Field | Control | Unit | Options / buckets |
|---|---|---|---|
| Price | range input | GBP £ | — |
| Mileage | range input | mi | — |
| Year | range input | year | — |
| Engine size | buckets | L | ≤1.2 / 1.3–1.6 / 1.7–2.0 / 2.1+ |
| Doors | multi | doors | 2 / 3 / 4 / 5 |
| Seats | multi | seats | 2 / 4 / 5 / 6+ |
| Power (bhp) | buckets | bhp | ≤120 / 121–180 / 181–240 / 240+ |
| Drivetrain | multi | — | FWD / RWD / AWD |
| Annual road tax | buckets | GBP £/yr | ≤£150 / £150–300 / £300–450 / £450+ |
| Insurance group | buckets | group 1–50 | 1–20 / 21–30 / 31–40 / 41–50 |
| Fuel economy | buckets | UK MPG | ≤35 / 35–45 / 45–55 / 55+ |
| CO₂ emissions | buckets | g/km | <100 / 100–150 / 150–200 / 200+ |
| Towing (braked) | buckets | kg / t | ≤750 kg / 750–1.5 t / 1.5–2.5 t / 2.5+ |
| Towing (unbraked) | buckets | kg | ≤400 kg / 400–750 kg / 750+ |
| Electric range | buckets | mi (WLTP) | <120 / 120–250 / 250+ |
| Fast-charge time | buckets | min | 30–60 / 60+ |
| Leased battery | toggle | — | Yes / No |
| Battery warranty | toggle | — | Yes / No |
| Popular features | multi | — | CarPlay · Bluetooth · Leather seats · Wheelchair access |
| Safety rating | multi | ★ stars | 5★ / 4★ / 3★ |
| Number of keys | multi | keys | 1 / 2 / 3+ |
| Has MOT | toggle | — | Yes / No |
| ULEZ compliant | toggle | — | Yes / No |
| Imported | toggle | — | Yes / No |
| Damage category | toggle | — | "exclude Cat N/S/C/D" |
| Vehicle usage | toggles | — | Not ex-hire · Not ex-fleet · Ex-demo only |
| Dealer rating | multi | ★ stars | 5★ / 4★+ / 3★+ |
| Dealer type | multi | — | Private / Independent / Franchise / Specialist |
| Part-exchange | toggle | — | Yes / No |
| Sold status | toggle | — | Live only (default hides sold) |
| Multiple images | toggle | — | 2+ images |
| Reduced | toggle | — | Reduced only |
| Recently added | buckets | time | 24 h / 7 d / 30 d |

### Grouping

Filters are rendered under visual parent sections, mirroring motors.co.uk: **Running costs · Performance · EV & hybrid · Features · Safety & condition · Vehicle history · Vehicle usage · Dealer · Advert options** (the last governs sold status, multiple-images, reduced and recently-added, exactly as the core rules require).

## Alternatives considered

| # | Alternative | Why rejected |
|---|---|---|
| 1 | Raw min/max input for *all* numeric fields | Fails the "obvious value + unit" test for tax, insurance group, towing, CO₂, BHP, EV range. |
| 2 | Single-unit market assumption only | Ignored cross-market metric ambiguity; contradicts ADR-0003 multi-market-readiness. Units are therefore pinned per label now, config-swappable later. |

## Consequences

**Positive:** buyers can filter by units/buckets they understand without knowing exact values; consistent with the strongest competitors; grouped sections match the core-rules requirement and keep the panel scannable.

**Negative:** more facet options than before → the accordion "collapse-all when searching" behaviour (Rev 1) plus per-section headers keep it usable; counts recompute per option (fine at PoC scale, re-measured in production per ADR-0002).

**Neutral / swappable:** the `ATTR_FACETS` table is data-only; each field's `b` (buckets), `e` (options) or raw-input style can be reconfigured by editing one row. A config flag can later flip any field between bucket and min/max without touching the engine. **Live-testing milestone:** after audience testing, confirm/revise bucket boundaries and unit defaults per market.

## Links / references

- ADR-0002 (facet engine — declarative, data-driven facets); ADR-0003 (provider/unit agnosticism)
- `docs/DATA-MODEL.md` (attr schema + sectioned facet table); `.clinerules/pia-v1-core-rules.md` (PoC Revision 2)
