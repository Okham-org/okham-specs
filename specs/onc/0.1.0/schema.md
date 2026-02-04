# ONC — Open Navigation Contract Schema

**Version**: 0.2.0  
**Status**: DRAFT

## Canonical Schema

Canonical schema: https://okham.org/schemas/onc/0.2.0/navigation.schema.json

## Schema Overview

The schema defines a navigation manifest with:
- `navigation.id` and `navigation.version`
- `screens[]` with `id`, `title`, and `componentRef`
- `routes[]` with `path` and `screenId`
- `nav.items[]` with `id`, `label`, and `route`
- Optional `guards[]` referencing OPC policy identifiers

## Notes

- The schema is DRAFT and may tighten in future releases.
- The schema uses open-world assumptions (`additionalProperties: true`).
