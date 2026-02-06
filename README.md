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
schemas/<contract>/<version>/manifest.json
specs/<contract>/<version>/spec.md
examples/<contract>/<version>/*
registry/contracts.json
registry/latest.json
```

### `schema.json` vs `manifest.json`

Within `schemas/<contract>/<version>/` we publish two different JSON files:

- `schema.json` (**normative**) — the canonical **JSON Schema** for the contract/version.
  Use this for deterministic validation (tooling, CI, compatibility checks).
- `manifest.json` (**descriptor / discovery**) — a small **manifest** that describes the published contract artifact and links to its canonical pieces:
  - contract id + version + status
  - relative pointers to `schema.json`, the spec (`spec.md`), and example files

**When to use which**

- If you want to validate data: use `schema.json`.
- If you want to discover “what exists” (and where the canonical files are): use `manifest.json` (or the top-level `registry/`).

**Normativity**

- The semantics and structure are defined by `specs/` + `schemas/` (normative).
- `manifest.json` is **not** a substitute for the spec/schema; it is a **descriptor** used by tooling and distribution.
  Treat its metadata and pointers as **authoritative for what is published**, but not as the normative definition of the contract itself.

### Directory intent

- `schemas/` are machine-consumable and normative (validation)
- `specs/` are human-readable and normative (requirements)
- `examples/` are informative
- `registry/` provides discovery metadata

---

## OAP: OKHAM Application Package

OAP defines the **application-level manifest** for an OKHAM app package.

- Schema: `https://okham.org/schemas/oap/0.1.0/schema.json`
- Spec (repo): `specs/oap/0.1.0/spec.md`
- Expected file in a package: `manifest/app.json`

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

Licensed under the Apache License 2.0.
See `LICENSE` and `NOTICE`.
