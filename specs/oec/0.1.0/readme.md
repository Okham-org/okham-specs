# Open Event Contract (OEC)

**Status**: DRAFT
**Version**: 0.1.0

## Problem This Contract Solves

Systems today emit events in proprietary formats with inconsistent metadata, versioning, and semantics. This creates:

- **Event schema fragmentation** — Each system defines events differently
- **Coupling to transport mechanisms** — Events tied to Kafka topics, NATS subjects, HTTP webhooks
- **No standard versioning** — Breaking changes appear without warning
- **Lost causality** — Event chains are untraceable across system boundaries
- **Classification chaos** — No consistent way to categorize event severity, domain, or lifecycle phase

**OEC provides a normative contract for events as observable, versioned, immutable facts that can be published and consumed across systems without transport coupling.**

Events are fundamental to distributed systems, but there is no interoperable standard for:
- What constitutes an event vs a command vs a message
- How events should be identified, versioned, and evolved

---

## Maturity

OEC is **DRAFT** for v0.0.1. Breaking changes are allowed. Schemas may tighten further in minor or major releases.
