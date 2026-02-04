# OCC Schema Reference

Quick reference for the OKHAM Capability Contract schema.

---

## Canonical Schema

Canonical schema: https://okham.org/schemas/occ/0.1.0/capability.schema.json

## Field Overview

Required fields:
- `contractVersion`: Must be `capability.contract.v1`
- `id`: Unique capability identifier (kebab-case)
- `name`: Human-readable name
- `version`: Semantic version
- `description`: Capability description

Common optional fields:
- `visibility`
- `tags`
- `category`
- `public`
- `icon`

## Non-Normative Excerpt

```json
{
  "contractVersion": "capability.contract.v1",
  "id": "user-management",
  "name": "User Management",
  "version": "1.0.0",
  "description": "Manages users",
  "visibility": "private"
}
```

## Notes

- This schema is DRAFT and may change before finalization.
- Additional sections (resources, subjects, subscriptions, HTTP, UI, MCP) remain permissive until a full schema is finalized.
