# Open Assertion Spec (OAS) Specification

**Version**: 0.1.0  
**Status**: DRAFT

## Abstract

This document specifies OAS (Open Assertion Spec). OAS defines portable, versioned *assertion primitives* that can be referenced by OLR rulesets and executed by tools (in any language) that emit OVR reports.

OAS exists to make the meaning of `rule.assert` (and the structure of `rule.params`) interoperable across implementations.

## Conformance

RFC 2119 key words apply.

## 1. Assertion definition

An OAS assertion definition MUST be a JSON/YAML object with:

- `oas` (REQUIRED): OAS contract version (SemVer)
- `assertionId` (REQUIRED): globally unique assertion id (namespaced)
- `version` (REQUIRED): assertion version (SemVer)
- `description` (REQUIRED): normative semantics
- `paramsSchema` (REQUIRED): JSON Schema for OLR `rule.params`

It MAY include:

- `name`
- `olr.assert`: the opcode used in OLR rules (`rule.assert`)
- `ovr.*`: guidance for producing OVR issues
- `examples[]`
- `metadata`

## 2. Relationship to OLR

- OLR rules are authored using `rule.assert` (opcode string) + `rule.params`.
- Tools SHOULD map `rule.assert` to an OAS `assertionId@version` through configuration or catalogs.
- Tools MAY also accept an explicit `rule.assertionRef` field in OLR (future extension) to bind directly.

## 3. Relationship to OVR

When an assertion fails, tools MUST emit an OVR issue. OAS provides guidance, but does not change OVR requirements.

## 4. Versioning

- OAS assertion versions MUST follow SemVer.
- Breaking changes to param shape or semantics MUST increment MAJOR.
