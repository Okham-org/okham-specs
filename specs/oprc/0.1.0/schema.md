# OPRC Schema

This document describes the JSON Schema for OPRC projection manifests. It is informational and does not replace the normative specification.

Canonical schema URL:
- https://okham.org/schemas/oprc/0.0.1/projection.schema.json

## Field Overview

Top-level fields:
- `oprc`: OPRC specification version.
- `id`: Projection identifier.
- `name`: Human-readable name.
- `description`: Projection purpose.
- `status`: Lifecycle status.
- `sources`: Source event bindings.
- `targets`: Projection targets.
- `mapping`: Mapping rules.
- `policyRefs` (optional): OPC policy references.

Source fields:
- `eventId`: OEC event identifier.
- `payloadType`: OTC payload type identifier.
- `filters` (optional): Equality/prefix filters.
- `causality` (optional): References to OEC causality fields.

Target fields:
- `targetId`: Output identifier.
- `kind`: `document` | `state` | `timeline` | `metric`.
- `mode`: `append` | `upsert`.
- `key` (optional): Upsert key path.
- `backend` (optional): Non-normative backend label.
- `outputType` (optional): OTC type for output shape.

Mapping fields:
- `rules`: Array of mapping rules with `targetId` and `targetPath`.
- Rules may use `copy`, `const`, or `concat` primitives.

## Schema Limitations

- The schema does NOT enforce `mode = upsert` requiring `key`. This is normative in `docs/tooling/oprc/SPECIFICATION.md`.
- The schema does NOT enforce mutual exclusivity of `copy`, `const`, or `concat`. This is normative in `docs/tooling/oprc/SPECIFICATION.md`.

For semantics and normative requirements, see [SPECIFICATION](SPECIFICATION.md).
