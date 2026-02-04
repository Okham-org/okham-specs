# Open Business Contract (OBC) Schema Reference

**Version**: 0.1.0  
**Status**: DRAFT  
**Last Updated**: 2026-01-17

---

## Canonical Schema

Canonical schema: https://okham.org/schemas/obc/0.1.0/flow.schema.json

## Field Overview

Required fields:
- `id`: Unique flow identifier
- `name`: Human-readable flow name
- `description`: Purpose description
- `steps`: Flow steps

Common optional fields:
- `version`
- `inputs`
- `outputs`
- `state`
- `policies`
- `metadata`

## Non-Normative Excerpt

```json
{
  "id": "order-fulfillment",
  "name": "Order Fulfillment",
  "description": "Fulfill a customer order",
  "steps": [
    { "id": "capture-payment", "type": "action", "description": "Charge payment" }
  ]
}
```

## Notes

- This schema is DRAFT and may change before finalization.
