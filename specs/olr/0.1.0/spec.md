# Open Linting Ruleset (OLR) Specification

**Version**: 0.1.0  
**Status**: DRAFT

## Abstract

This document specifies the Open Linting Ruleset (OLR). OLR defines a portable, tool-agnostic ruleset format that can be executed by different linters/validators while producing interoperable results (OVR).

## Conformance

The key words "MUST", "SHOULD", and "MAY" are to be interpreted as described in RFC 2119.

## 1. Ruleset object

An OLR ruleset MUST be a YAML or JSON document representing a single JSON object with:

- `rulesetId` (REQUIRED): globally unique id (namespaced; e.g., `okham.core.mvp`).
- `version` (REQUIRED): SemVer string.
- `description` (OPTIONAL): human-readable summary.
- `metadata` (OPTIONAL): free-form object.
- `rules` (REQUIRED): array of rule objects.

## 2. Rule object

Each rule MUST declare:

- `ruleId` (REQUIRED): unique within the ruleset.
- `severity` (REQUIRED): one of `error`, `warn`, `info`.
- `scope` (REQUIRED): logical domain identifier (e.g., `global`, `otc`, `oec`, `oucc`, `okham-app`).
- `selector` (REQUIRED): file/document selection constraints.
- `assert` (REQUIRED): assertion opcode/kind identifier.
- `assertionRef` (OPTIONAL): reference to an OAS assertion definition (`assertionId` + `version`).
- `params` (REQUIRED): object of assertion parameters.
- `message` (REQUIRED): human-facing error message.

Optional:

- `suggestedFix`: non-executable remediation advice.
- `tags`: list of strings.

## 3. Selector

A selector MUST include:

- `globs` (REQUIRED): list of glob patterns.

A selector MAY include:

- `documentKind`: tool-defined document kind discriminator.

## 4. Assertion kinds and OAS

`assert` is a string executed by the tool (e.g., `required_field`, `pattern_match`, `ref_exists`).

If `assertionRef` is present, it MUST point to an OAS assertion definition (`assertionId` + `version`) that defines:

- the normative semantics
- the expected `params` shape (`paramsSchema`)

Tools MAY validate `params` against the referenced OAS `paramsSchema`.

If `assertionRef` is absent, assertion semantics remain tool-defined.

Tools MUST:

- treat unknown `assert` kinds as unsupported
- report unsupported-rule execution as OVR issues with a tool-defined rule id or an `info` entry

## 5. Determinism

For the same inputs, tools SHOULD execute rules deterministically. Tools SHOULD preserve input rule ordering when generating OVR issues.
