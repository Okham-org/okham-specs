# OCS — Open Conformance Suite

## Version 0.1.0
## Status: DRAFT

OCS defines a **portable, language-agnostic** format for conformance test suites.

The goal: enable multiple independent implementations (Go/Node/Python/Rust/remote services) to validate the same contract artifacts (schemas, rulesets, assertions) and produce consistent outcomes.

### What is a conformance suite?
A conformance suite is a set of **test cases** (inputs) plus **expected outcomes**.

- OCS is not a linter engine.
- OCS is not a ruleset.
- OCS is the *test harness contract* that engines can consume.

### Canonical schema
- `https://okham.org/schemas/ocs/0.1.0/schema.json`

### Minimal structure
An OCS document MUST include:
- `ocs` (version)
- `suiteId`
- `target` (what artifact is being validated)
- `cases[]`

### Target kinds
- `olr.ruleset` — validate a ruleset implementation
- `oas.assertion` — validate an assertion implementation
- `schema` — validate a schema validator

### Case expectations
Each case declares:
- `path` — relative file path to the input
- `expect` — pass|fail
- `expectRuleIds` — required when `expect=fail`

### Notes
OCS is intentionally small. Engines may add features (snapshots, baselines, richer evidence checks), but OCS stays focused on interoperability.
