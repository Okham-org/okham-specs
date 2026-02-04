# OKHAM ODT — Open Design Tokens
- Contract: ODT
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/odt/0.1.0/schema.json
- Last Updated: 2026-01-17

---

## Abstract

This document specifies ODT, a neutral, machine-readable format for exchanging design tokens across systems. It defines the required structure of token bundles, tokens, references, and metadata.

---

## Conformance

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

---

## 1. Token Bundle

A token bundle MUST be a JSON object with the following fields:

- `odt` (REQUIRED): ODT version string (SemVer)
- `id` (REQUIRED): Token bundle identifier (namespaced)
- `name` (REQUIRED): Human-readable bundle name
- `description` (REQUIRED): Bundle description
- `tokens` (REQUIRED): Array of token definitions
- `metadata` (OPTIONAL): Bundle metadata

Token bundle identifiers MUST follow the pattern `namespace.domain.name`.

---

## 2. Token Definition

Each token MUST include:

- `id` (REQUIRED): Token identifier, unique within the bundle
- `type` (REQUIRED): Token category (enum)
- `value` (REQUIRED): Token value or alias reference
- `description` (OPTIONAL): Human-readable description
- `status` (OPTIONAL): `active`, `deprecated`, or `experimental`
- `extensions` (OPTIONAL): Additional metadata

Token identifiers MUST use kebab-case.

---

## 3. Token Types

ODT defines the following token types:

- `color`
- `typography`
- `spacing`
- `radius`
- `elevation`
- `motion`
- `opacity`

Token types MAY be extended by tooling via the `extensions` field but MUST NOT alter the base type semantics.

---

## 4. Token Values

Token values MUST be one of:

- A literal value appropriate for the token type
- An alias object referencing another token by id

Alias objects MUST use:

```json
{
  "alias": "token-id"
}
```

---

## Closed-World Schema Note (v0.0.1)

The ODT JSON Schema uses `additionalProperties: false` at the root and for token definitions. This closed-world assumption is intentional for v0.0.1. Future extensibility will require a minor or major version bump. This is a stability decision, not an oversight.
