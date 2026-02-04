# Open Intent Contract (OIC) Schema Reference

**Version**: 0.1.0  
**Status**: DRAFT

---

## Canonical Schema

Canonical schema: https://okham.org/schemas/oic/0.1.0/intent.schema.json

## Field Overview

- `oic`: OIC version (semver)
- `id`: Unique intent declaration identifier
- `intent`: Intent token from the OIC registry
- `purpose`: Human-readable purpose statement
- `constraints`: Optional declarative constraints
- `expectations`: Optional expected outcomes
- `transitions`: Optional allowed next intent transitions

## Non-Normative Excerpt

```json
{
  "oic": "0.1.0",
  "id": "intent-42",
  "intent": "decide",
  "purpose": "Choose whether to close the ticket"
}
```

## Notes

- This schema is DRAFT and may change before finalization.
