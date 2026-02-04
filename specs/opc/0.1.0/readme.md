# Open Policy Contract (OPC)

**Status**: DRAFT
**Version**: 0.1.0

## Problem This Contract Solves

Systems today enforce policies through hardcoded business logic, scattered authorization checks, and framework-specific rule engines. This creates:

- **Policy fragmentation** — Rules buried in application code across services
- **No declarative policy language** — Policies cannot be expressed as data
- **Audit nightmares** — Cannot determine what policies were active at any point in time
- **Inconsistent enforcement** — Same rule implemented differently across systems
- **No policy versioning** — Policy changes are deployed as code changes without traceability

**OPC provides a normative contract for declarative policies that constrain, allow, deny, or condition behavior in a system without coupling to execution engines.**

Every system needs policies (authorization, rate limiting, data governance, compliance), but there is no interoperable standard for:
- What constitutes a policy vs a business rule
- How policies should be identified, versioned, and scoped

---

## Maturity

OPC is **DRAFT** for v0.0.1. Breaking changes are allowed. Schemas may tighten further in minor or major releases.
