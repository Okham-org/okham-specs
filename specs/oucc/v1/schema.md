# UI Components Schema

## Overview

This document defines the JSON Schema for UI component declarations in OKHAM capabilities, including rendering profiles and artifact references.

## Status: DRAFT (v0.1.0)

## Canonical Schema

Canonical schema: https://okham.org/schemas/oucc/0.1.0/component.schema.json

## Field Overview

Required fields:
- `component`: OUCC schema version
- `id`: Unique component ID
- `name`: Human-readable name
- `version`: Component semantic version

Optional fields:
- `description`
- `status`: draft | stable | deprecated | removed
- `category`: layout | form | data-display | navigation | feedback
- `tags`
- `props`
- `slots`
- `events`
- `profiles`
- `i18n`
- `a11y`

## Non-Normative Excerpt

```json
{
  "component": "0.1.0",
  "id": "okham_ui_card",
  "name": "Card",
  "version": "1.0.0",
  "profiles": [
    {
      "engine": "templ",
      "artifact": {
        "kind": "file",
        "path": "templ/card.templ",
        "sha256": "a1b2c3d4e5f6789abcdef0123456789abcdef0123456789abcdef0123456789"
      }
    }
  ]
}
```

## Notes

- This schema is DRAFT and may change before finalization.
- Artifact kinds beyond `file` are reserved and remain permissive.
