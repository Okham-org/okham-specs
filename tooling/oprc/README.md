# OPRC — Open Projection Contract

**Category**: Tooling & Format Standard
**Version**: 0.0.1
**Status**: DRAFT

“OPRC — Open Projection Contract: Declarative specification of projections that consume OEC events and OTC payloads to materialize read models (views/indices), independent of storage engine and query language.”

## Why OPRC Exists

Modern systems often need read-optimized views (indexes, timelines, state tables) without changing the write model. OPRC defines a declarative manifest for those projections so tooling can validate, catalog, and govern them without assuming a specific runtime or backend.

## Relationship to Other Standards

- “OEC provides facts; OPRC materializes views of those facts.”
- “OTC defines payload meaning; OPRC maps payloads into projection outputs.”
- “OPC may govern projection execution and access, but is not embedded by OPRC.”
- “OCC may package projection capabilities; OPRC only defines the manifest.”

## Non-Goals

OPRC does NOT define:
- Execution scheduling or streaming semantics.
- Guaranteed delivery or replay semantics.
- Distributed transactions.
- Query language standardization.
- Storage backend standardization.
- Join semantics.

## Documents

- [SPECIFICATION](SPECIFICATION.md) — Normative definition of the projection manifest.
- [SCHEMA](SCHEMA.md) — Schema overview and canonical URL.
- [EXAMPLES](EXAMPLES.md) — Non-normative examples.

## Status

OPRC is a tooling-level standard in **DRAFT** status (v0.0.1). It does not modify OKHAM v0.1.0 Core semantics and defines only the manifest format.
