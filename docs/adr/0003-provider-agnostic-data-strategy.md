# ADR-0003: Provider-agnostic data strategy (persistence & migration)

## Status

Accepted

## Context

Core rules demand: *"the database and its assets must be easily migratable across any provider — e.g. on-prem to Azure — with minimal issues; as straightforward as possible export/import."* No provider must be a trap.

Design decisions needed now because the entire data model (DATA-MODEL.md), the PoC code, and every future ADR consume the storage contract — a reversal later would be expensive.

## Decision

- **The PoC has NO database.** All catalogue data is deterministic in-code seed data; all *user-generated* state (posted ads, lead enquiries) lives in **browser `localStorage`**. This is sufficient for concept validation and keeps deployment void-free (ADR-0001).
- **The conceptual contract is a provider-neutral document (JSON) schema** — tables, fields, enumerations and value objects are defined *without* any provider-specific mapping: no Firestore `FieldValue`-isms, no Postgres `SERIAL`, no Azure Tables partition keys. Fields are JSON-shaped so any store can hold them.
- **Ambiguity units with value objects.** Currency and quantity/unit fields (GBP/EUR, miles/km, mpg/L per 100 km) are modelled as configured value objects, not bare numbers, so a future market expansion plugs in without a data migration (see DATA-MODEL.md §Value Objects).
- **The migration seam is an import/export contract**, defined once in DATA-MODEL.md: any persistence provider must expose `exportDataset() → JSON blob` and `importDataset(JSON)` that round-trip the schema losslessly. "Straightforward export/import" then holds by definition, and a **provider decision ADR (ADR-+++) is explicitly deferred** until v1 build time, when we will re-evaluate Postgres vs Firestore vs SQLite vs Azure etc. against that contract.

## Alternatives considered

| # | Alternative | Why rejected / deferred |
|---|---|---|
| 1 | Choose a provider now (SQLite/Firestore/Postgres) and wire the PoC to it | Adds cost, credentials, migration risk and contradicts the zero-ops PoC. Does not serve any hypothesis we need to test in v0.1. |
| 2 | Abstract persistence behind a storage-interface in the PoC (swap localStorage→API) | Premature abstraction for a PoC; the seed data is already replaceable at the collection boundary. Noted as a refactor candidate for v1 rather than built now. |
| 3 | Delay *all* data-model design | The PoC's facet engine and seeded data ARE the model; leaving it implicit costs the strongest-clean data contract for v1. |

## Consequences

**Positive:** the PoC stays zero-infra while the schema is provider-agnostic; migrate-friction is a designed-out concern (import/export contract); currency/location portability is baked into the value objects, not retrofitted.

**Negative:** any future real provider must still be evaluated against the export/import contract (an ADR-sized decision); "document store validation" work implicit in the schema is currently unpaid debt.

**Neutral:** SQL vs document vs key-value remains fully open at v1 — nothing in DATA-MODEL.md forecloses it.

## Links / references

- ADR-0001 (PoC statics), ADR-0002 (facet engine), `docs/DATA-MODEL.md`, `docs/PoC-PRODUCTION-READINESS.md`.