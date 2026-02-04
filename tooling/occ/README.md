# OCC — Open Capability Contract

## Version: 0.1.0
## Status: DRAFT

**OCC** (Open Capability Contract) is a declarative specification for defining
capabilities in the OKHAM ecosystem. It describes what a capability exposes,
its resources, interfaces, and behavior — independent of implementation.

---

## What is OCC?

Open Capability Contract is a YAML-based declaration that defines:

- **Identity**: What the capability is (id, name, version, description)
- **Resources**: NATS streams, KV buckets, and subjects it uses
- **Interfaces**: HTTP endpoints and MCP tools it exposes
- **UI**: Admin pages, navigation, and components
- **Governance**: Limits, auth, and lifecycle hooks

---

## Maturity

OCC is **DRAFT** for v0.0.1. Breaking changes are allowed. Schemas may tighten further in minor or major releases.
