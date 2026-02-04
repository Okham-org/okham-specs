# Open Policy Contract (OPC) — Schema Reference

**Version**: 0.1.0

This document provides a quick reference for the OPC schema structure.

---

## Canonical Schema

Canonical schema: https://okham.org/schemas/opc/0.1.0/policy.schema.json

## Field Overview

- `policy`: OPC version (semantic version)
- `id`: Policy identifier (unique, namespaced)
- `name`: Human-readable policy name
- `version`: Policy version
- `status`: Lifecycle status (`draft`, `active`, `deprecated`, `removed`)
- `scope`: Policy scope (`global`, `service`, `capability`)
- `target`: Optional applicability target
- `condition`: Condition expression and inputs
- `decision`: Decision outcomes (allow, deny, constrain, warn)
- `metadata`: Optional free-form metadata

## Non-Normative Excerpt

```json
{
  "policy": "0.1.0",
  "id": "auth.access-control.user-data-access",
  "name": "User Data Access Policy",
  "version": "1.0.0",
  "status": "draft",
  "scope": "global",
  "condition": {
    "expression": "subject.id == resource.owner_id",
    "inputs": ["subject.id", "resource.owner_id"],
    "language": "cel"
  },
  "decision": {
    "allow": { "message": "User authorized" }
  }
}
```

## Notes

- This schema is DRAFT and may change before finalization.
- At least one decision outcome should be present.
