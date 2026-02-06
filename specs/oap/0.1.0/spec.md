# OAP — OKHAM Application Package Manifest (0.1.0)

Status: **draft**

OAP defines the **application-level manifest** for an OKHAM app package.

This manifest is **informative** (non-normative for runtime behavior), but it is **normative** for:

- establishing the package `id` and `version`
- establishing the package `namespace` used as a stable prefix for contract `id`s

## File location

The manifest is expected at:

- `manifest/app.json`

## Fields

### `id` (required)

Application identifier.

### `version` (required)

Semver version of the manifest.

### `name` (required)

Human readable app name.

### `namespace` (required)

Stable namespace prefix for contract ids in this package.

Example:

- `app.okham.todo`

### Optional fields

- `summary`: short one-line summary
- `goals`: list of goals
- `non_goals`: explicit boundaries
- `actors`: list of actors/roles ({ id, description })
- `notes`: free-form notes

## Relationship to runtime-agnosticity

Runtime-specific details (Cloudflare, NATS, Kafka, Docker, etc.) MUST NOT be embedded in core contracts.
They may appear under `bindings/` or `deploy/` directories.
