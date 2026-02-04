# OKHAM OPRC — Open Projection Contract
- Contract: OPRC
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/oprc/0.1.0/schema.json
- Category: Tooling & Format Standard

“OPRC — Open Projection Contract: Declarative specification of projections that consume OEC events and OTC payloads to materialize read models (views/indices), independent of storage engine and query language.”

## 1. Purpose and Scope

OPRC defines a declarative manifest for describing projections that turn OEC events with OTC payloads into read-optimized views. OPRC is a tooling-level format; it does not define runtime behavior, execution engines, or storage implementations.

Relationship statements:
- “OEC provides facts; OPRC materializes views of those facts.”
- “OTC defines payload meaning; OPRC maps payloads into projection outputs.”
- “OPC may govern projection execution and access, but is not embedded by OPRC.”
- “OCC may package projection capabilities; OPRC only defines the manifest.”

## 2. Conformance

An OPRC manifest is compliant if it:
- Conforms to the projection manifest structure in this specification.
- Uses only the mapping primitives defined in this version.
- References OEC and OTC identifiers for sources.
- Declares targets with supported kinds and modes.

Implementations MAY provide additional, non-conflicting fields. Such fields MUST NOT change the meaning of the normative fields defined here.

## 3. Manifest Structure (Normative)

An OPRC manifest MUST be a JSON object with the following top-level fields:
- `oprc` (string): OPRC specification version.
- `id` (string): Projection identifier (unique within a catalog).
- `name` (string): Human-readable name.
- `description` (string): Purpose of the projection.
- `status` (string): Lifecycle status (e.g., `draft`, `stable`, `deprecated`, `removed`).
- `sources` (array): Source event bindings.
- `targets` (array): Projection outputs.
- `mapping` (object): Mapping rules from source inputs to target outputs.
- `policyRefs` (array, optional): OPC policy identifiers governing execution or access.

### 3.1 Sources

Each `sources` entry MUST contain:
- `eventId` (string): OEC event identifier.
- `payloadType` (string): OTC type identifier expected in the event payload.

Each `sources` entry MAY contain:
- `filters` (array): Minimal filters with equality or prefix semantics.
- `causality` (object): References to OEC causality fields. OPRC does not define causality semantics; it only allows referencing them.

Filters MUST be simple and declarative. Each filter entry:
- MUST declare a `path`.
- MAY declare `equals` or `prefix` values.

OPRC does NOT define a query language. Implementations MUST treat filters as simple comparisons and MUST NOT introduce custom query syntaxes in v0.0.1.

### 3.2 Targets

Each `targets` entry MUST contain:
- `targetId` (string): Identifier of the projection output.
- `kind` (enum): One of `document`, `state`, `timeline`, `metric`.
- `mode` (enum): One of `append`, `upsert`.

Each `targets` entry MAY contain:
- `key` (string): Source path used as the upsert key.
- `backend` (string): Non-normative backend label (e.g., `postgres-jsonb`).
- `outputType` (string): OTC type identifier describing the output shape.

If `mode` is `upsert`, `key` MUST be supplied. OPRC does not standardize how keys are enforced beyond this declaration.

### 3.3 Mapping

`mapping` MUST contain a `rules` array. Each rule MUST declare:
- `targetId` (string): The target the rule writes to.
- `targetPath` (string): Target field path.

Each rule MAY also declare:
- `sourceEventId` (string): Limit the rule to a specific source event.
- `copy` (string): Copy a value from a source path.
- `const` (any): Use a constant value.
- `concat` (array): Concatenate string fragments.

Mapping rules MUST use exactly one of `copy`, `const`, or `concat` in v0.0.1. The schema does not enforce this restriction; it is a normative requirement.

Source paths MUST be expressed as JSON Pointer (RFC 6901) or simple dotted paths. Implementations MUST interpret paths against a source envelope with two top-level keys:
- `event`: the OEC envelope metadata (id, timestamp, causality, classification).
- `payload`: the OTC payload data.

### 3.4 Validation Hooks

Implementations MUST validate source inputs against referenced OEC and OTC schemas when available. Targets MAY declare `outputType` to enable validation of output shapes, but `outputType` is optional in v0.0.1.

### 3.5 Policy Hooks

If `policyRefs` are present, implementations MAY evaluate them before executing or exposing projections. OPRC itself does not embed or execute OPC policies.

## 4. Compliance Requirements

An implementation is OPRC-compliant if it:
- Accepts manifests that match this specification.
- Applies mapping rules using the defined primitives.
- Treats targets and sources as declarative descriptors rather than executable logic.
- Does not require a specific storage engine or query language.

## 5. Out of Scope

OPRC does NOT define:
- Execution scheduling or streaming semantics.
- Guaranteed delivery or replay semantics.
- Distributed transactions.
- Query languages or query execution.
- Storage backend specifications.
- Join semantics or cross-entity aggregation rules.

## 6. Status

OPRC is DRAFT in v0.0.1. Backward-incompatible changes MAY occur before stabilization.
