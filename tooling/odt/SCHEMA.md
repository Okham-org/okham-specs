# ODT — Open Design Tokens Schema

**Version**: 0.1.0  
**Status**: DRAFT  
**Last Updated**: 2026-01-17

---

## Canonical Schema

Canonical schema: https://okham.org/schemas/odt/0.1.0/tokens.schema.json

## Field Overview

Required fields:
- `odt`: ODT version (semver)
- `id`: Unique namespaced identifier
- `name`: Human-readable name
- `description`: Purpose description
- `tokens`: Token definitions

Common optional fields:
- `metadata`

## Non-Normative Excerpt

```json
{
  "odt": "0.1.0",
  "id": "okham.design.system",
  "name": "OKHAM Design Tokens",
  "description": "Base design tokens",
  "tokens": [
    { "id": "color-primary", "type": "color", "value": "#0FA958" }
  ]
}
```

## Notes

- This schema is DRAFT and may change before finalization.
