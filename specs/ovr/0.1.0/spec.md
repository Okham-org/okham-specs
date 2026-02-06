# OKHAM OVR — Open Validation Report
- Contract: OVR
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/ovr/0.1.0/schema.json
- Last Updated: 2026-01-17

---

## Abstract

This document specifies the Open Validation Report (OVR). OVR defines a portable JSON report format for linting and validation results across OKHAM artifacts.

---

## Conformance

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

---

## 1. Report Object

An OVR report MUST be a JSON object with the following top-level members:

- `ovr` (REQUIRED): OVR version string (SemVer)
- `run` (REQUIRED): Run metadata
- `summary` (REQUIRED): Summary of issue counts
- `issues` (REQUIRED): Array of issue objects
- `metadata` (OPTIONAL): Additional report metadata

### 1.1 Run Metadata

The `run` object MUST include:

- `runId` (REQUIRED): Unique identifier for this run
- `timestamp` (REQUIRED): ISO 8601 timestamp in UTC
- `tool` (REQUIRED): Tool identifier (e.g., `okham-lint`)
- `rulesets` (REQUIRED): Array of applied ruleset references (`rulesetId` + `version`)
- `target` (REQUIRED): Target path used for linting
- `baseline` (OPTIONAL): Baseline path when compatibility checks are applied

---

## 2. Summary Object

The `summary` object MUST include:

- `errors` (REQUIRED): Total error count
- `warnings` (REQUIRED): Total warning count
- `infos` (REQUIRED): Total info count

Counts MUST reflect the `issues` array.

---

## 3. Issue Object

Each entry in `issues` MUST include:

- `ruleId` (REQUIRED): Identifier of the rule that triggered the issue
- `severity` (REQUIRED): `error`, `warn`, or `info`
- `message` (REQUIRED): Human-readable description
- `file` (REQUIRED): Relative file path
- `jsonPointer` (REQUIRED): JSON Pointer to the location in the document
- `evidence` (REQUIRED): Value or snippet found at the location

Optional members:

- `line` (OPTIONAL): Line number (1-based)
- `column` (OPTIONAL): Column number (1-based)
- `suggestedFix` (OPTIONAL): Non-executable suggestion
- `source` (OPTIONAL): Tool source identifier

---

## 4. Severity Levels

OVR defines three severity levels:

- `error` — Must be fixed for compliance
- `warn` — Should be fixed, non-blocking
- `info` — Informational

---

## 5. Determinism

OVR reports MUST be deterministic for the same inputs. Tools SHOULD sort `issues` by file path, jsonPointer, and ruleId.

---

## 6. Ruleset Definition Status

OVR reports reference external rulesets.

- In OVR `run.rulesets[]`, each entry MUST include `rulesetId` and `version`.
- The ruleset format is specified by OLR.
  - Canonical OLR schema: `https://okham.org/schemas/olr/0.1.0/schema.json`

---

## 7. Versioning

OVR versions MUST use semantic versioning. Breaking changes require a major version increment.

---

## References

- [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119)
- [JSON Schema](https://json-schema.org/)
