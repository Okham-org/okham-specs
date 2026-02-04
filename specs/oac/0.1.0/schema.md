# Open Accessibility Contract (OAC) Schema Reference

**Version**: 0.1.0  
**Status**: DRAFT  
**Last Updated**: 2026-01-17

---

## Canonical Schema

Canonical schema: https://okham.org/schemas/oac/0.1.0/a11y.schema.json

## Field Overview

Required fields:
- `id`: Unique obligation identifier
- `target`: Component or interaction identifier
- `standard`: Accessibility standard reference
- `requirements`: Accessibility criteria

Common optional fields:
- `priority`
- `evidence`
- `exemptions`
- `metadata`

## Non-Normative Excerpt

```json
{
  "id": "keyboard-navigation",
  "target": "modal-dialog",
  "standard": "WCAG 2.1 AA",
  "requirements": [
    { "criterion": "2.1.1", "description": "All functionality available from keyboard" }
  ]
}
```

## Notes

- This schema is DRAFT and may change before finalization.
- Additional evidence and semantic role schemas remain in draft form and may be published in future versions.
