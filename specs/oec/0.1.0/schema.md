# Open Event Contract (OEC) — Schema Reference

**Version**: 0.1.0

This document provides a quick reference for the OEC schema structure.

---

## Canonical Schema

Canonical schema: https://okham.org/schemas/oec/0.1.0/event.schema.json

## Field Overview

- `event`: OEC version (semantic version)
- `id`: Event identifier (unique, namespaced)
- `name`: Human-readable event name (past tense)
- `version`: Event schema version
- `status`: Lifecycle status (`draft`, `stable`, `deprecated`, `removed`)
- `source`: Event source information
- `timestamp`: ISO 8601 timestamp (UTC)
- `causality`: Optional causation and correlation metadata
- `classification`: Optional classification metadata
- `payload`: Event data with OTC type reference
- `metadata`: Optional free-form metadata

## Non-Normative Excerpt

```json
{
  "event": "0.1.0",
  "id": "order.payment.processed",
  "name": "Order Payment Processed",
  "version": "1.2.0",
  "status": "draft",
  "source": {
    "capability": "payment-service"
  },
  "timestamp": "2026-01-16T10:30:00.123Z",
  "payload": {
    "type": "https://okham.org/types/payment/1.0.0",
    "data": {}
  }
}
```

## Notes

- This schema is DRAFT and may change before finalization.
- Payload `data` must conform to the referenced OTC type.
