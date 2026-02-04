# OKHAM OSR — Open Snippet Registry
- Contract: OSR
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/osr/0.1.0/schema.json
- Last Updated: 2026-01-17

---

## Abstract

This document specifies OSR, a format for describing and distributing reusable snippets through interoperable registries. It defines registry manifests, snippet entries, and payload metadata.

---

## Conformance

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

---

## 1. Registry Manifest

A registry manifest MUST be a JSON object with the following fields:

- `osr` (REQUIRED): OSR version string (SemVer)
- `id` (REQUIRED): Registry identifier (namespaced)
- `name` (REQUIRED): Registry name
- `description` (REQUIRED): Registry description
- `snippets` (REQUIRED): Array of snippet entries
- `metadata` (OPTIONAL): Registry metadata

Registry identifiers MUST follow the pattern `namespace.domain.name`.

---

## 2. Snippet Entry

Each snippet entry MUST include:

- `id` (REQUIRED): Snippet identifier
- `name` (REQUIRED): Snippet name
- `version` (REQUIRED): Snippet version (SemVer)
- `language` (REQUIRED): Primary language or format
- `content` (REQUIRED): Snippet payload

Optional fields:
- `description`
- `license`
- `tags`
- `dependencies`
- `compatibility`

Snippet identifiers MUST use kebab-case.

---

## 3. Content Payloads

The `content` field MUST be one of:

- A string payload
- An object with `format` and `body`

Example:

```json
{
  "format": "yaml",
  "body": "id: example\nname: Sample"
}
```

---

## 4. Metadata

The `metadata` object MAY include:
- `source`
- `updated_at`
- `tags`

---

## Closed-World Schema Note (v0.0.1)

The OSR JSON Schema uses `additionalProperties: false` at the root and for snippet definitions. This closed-world assumption is intentional for v0.0.1. Future extensibility will require a minor or major version bump. This is a stability decision, not an oversight.
