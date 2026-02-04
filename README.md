# OKHAM Specifications

This repository is the **authoritative source of truth** for the OKHAM open standards.

It contains the **normative specifications**, **canonical JSON Schemas**, and **reference examples**
that define how OKHAM contracts are structured and validated.

Human-facing documentation is published at **https://okham.org**.
This repository is focused on **precision, stability, and machine consumption**.

---

## Repository scope

This repository defines:

- Normative specifications (`/specs`)
- Canonical JSON Schemas (`/schemas`)
- Reference examples (`/examples`)
- Contract registry metadata (`/registry`)

This repository does **not** contain:

- Implementations
- Tooling
- Runtimes
- UI components
- Deployment artifacts

Those live in separate repositories and products (e.g. okham.io).

---

## Canonical URLs

All schemas published in this repository are served under canonical URLs of the form:

```
https://okham.org/schemas/<contract>/<version>/schema.json
```

These URLs are considered **stable identifiers** for validation and tooling.

GitHub is used for contribution and version control.
The public OKHAM domain is the canonical distribution point.

---

## Repository structure

```
schemas/<contract>/<version>/schema.json
schemas/<contract>/<version>/contract.json
specs/<contract>/<version>/spec.md
examples/<contract>/<version>/*
registry/contracts.json
registry/latest.json
```

- `schemas/` are machine-consumable and normative
- `specs/` are human-readable and normative
- `examples/` are informative
- `registry/` provides discovery metadata

---

## Versioning and status

Each contract version declares a status:

- `draft`: subject to change
- `stable`: backward-compatible evolution only
- `deprecated`: retained for compatibility, not recommended

The latest and stable versions of each contract are declared in:

- `registry/latest.json`

---

## Contribution model

This repository follows a **spec-first, contract-driven** model.

Changes to specifications or schemas:

- MUST be made via pull requests
- SHOULD include updated examples
- MUST preserve backward compatibility unless a new major version is introduced

Breaking changes are only allowed in:

- new major versions
- contracts explicitly marked as `draft`

---

## License

See `LICENSE`.
