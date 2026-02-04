# Open Type Contract (OTC) Schema Reference

**Version**: 0.1.0  
**Status**: DRAFT  
**Last Updated**: 2026-01-14

---

## Canonical Schemas

- Type schema: https://okham.org/schemas/otc/0.1.0/type.schema.json
- Meaning tag schema: https://okham.org/schemas/otc/0.1.0/meaning-tag.schema.json
- Meaning domains schema: https://okham.org/schemas/otc/0.1.0/meaning-domains.schema.json

## Field Overview (Type Schema)

Required fields:
- `otc`: OTC version (semver)
- `id`: Unique namespaced identifier
- `name`: Human-readable name
- `description`: Purpose description
- `fields`: Field definitions

Common optional fields:
- `version`
- `status`
- `schema`
- `extends`
- `mixins`
- `abstract`
- `deprecated`
- `deprecatedReason`
- `tags`
- `metadata`
- `implementations`
- `examples`

## Non-Normative Excerpt

```json
{
  "otc": "0.1.0",
  "id": "okham.core.user",
  "name": "User",
  "description": "User identity record",
  "fields": [
    {
      "name": "email",
      "type": "string",
      "meaning": "person.email"
    }
  ]
}
```

## Notes

- These schemas are DRAFT and may change before finalization.
- The type schema references additional hint/constraint structures; these remain permissive where unspecified.
