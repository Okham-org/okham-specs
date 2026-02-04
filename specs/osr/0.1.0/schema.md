# OSR — Open Snippet Registry Schema

**Version**: 0.1.0  
**Status**: DRAFT  
**Last Updated**: 2026-01-17

---

## Canonical Schema

Canonical schema: https://okham.org/schemas/osr/0.1.0/snippet.schema.json

## Field Overview

Required fields:
- `osr`: OSR version (semver)
- `id`: Unique namespaced identifier
- `name`: Human-readable name
- `description`: Purpose description
- `snippets`: Snippet definitions

Common optional fields:
- `metadata`

## Non-Normative Excerpt

```json
{
  "osr": "0.1.0",
  "id": "okham.snippets.core",
  "name": "Core Snippets",
  "description": "Reusable snippets",
  "snippets": [
    {
      "id": "create-user",
      "name": "Create User",
      "version": "1.0.0",
      "language": "yaml",
      "content": "..."
    }
  ]
}
```

## Notes

- This schema is DRAFT and may change before finalization.
