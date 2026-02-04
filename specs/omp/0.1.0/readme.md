# OMP — OKHAM MCP Profile

**Version**: 0.1.0  
**Status**: DRAFT  
**Category**: Supporting Integration Standard

---

## What is OMP?

**OMP (OKHAM MCP Profile)** is a supporting integration standard that specifies how OKHAM exposes its authoritative standards through the **Model Context Protocol (MCP)**.

OMP is **NOT**:
- A core contract (it does not define system semantics)
- A replacement for MCP (it is a profile of MCP)
- A new protocol (it uses MCP as-is)

OMP **IS**:
- A stable, read-only MCP profile for OKHAM standards access
- A specification of required MCP tools and their behaviors
- A guarantee of deterministic, authoritative responses

> **Key Principle**: MCP defines how agents communicate. OMP defines what OKHAM exposes through MCP.

---

## Why OMP Exists

OKHAM standards are designed to be **machine-consumable** and **LLM-implementable**. OMP makes this possible by:

1. **Eliminating hallucination**: LLMs can retrieve authoritative documentation instead of guessing
2. **Enabling deterministic implementation**: Schemas and examples are always available
3. **Providing discovery**: Agents can explore OKHAM standards programmatically
4. **Ensuring version clarity**: Every response includes version and status metadata

Without OMP, LLMs would need to:
- Guess at standard structures
- Invent semantics not present in specs
- Mix responsibilities between contracts
- Assume implementation details

With OMP, LLMs can:
- Retrieve exact specifications
- Validate artifacts against schemas
- Resolve terminology unambiguously
- Implement interoperability confidently

---

## Relationship to MCP

**MCP (Model Context Protocol)** is a general-purpose protocol for LLM-agent communication. It defines:
- How tools are exposed
- How resources are accessed
- How prompts are structured

**OMP** is a **profile** of MCP that specifies:
- Which MCP tools OKHAM servers MUST expose
- What parameters and responses look like
- What guarantees are provided (authority, versioning, determinism)

OMP does not change MCP. It uses MCP's existing tool and resource mechanisms to expose OKHAM standards in a standardized way.

> **OMP defines a general pattern for machine access to authoritative specifications.**  
> **OKHAM is the first application domain of this profile.**

This means:
- OMP is **read-only** — it exposes authority, not behavior
- OMP does **not redefine MCP** — it uses MCP as-is
- OMP can be **applied to other standards bodies** — the pattern is domain-agnostic

---

## What OMP Standardizes

OMP defines:

### 1. Discovery & Connection
- Base endpoint convention (e.g., `/mcp/`)
- Profile identifier and version (`omp_version`)
- Health endpoint
- Capability discovery

### 2. Required MCP Tools
- `list_contracts` — List all OKHAM contracts
- `get_contract_doc` — Retrieve contract documentation
- `get_schema` — Retrieve JSON Schema for a contract
- `get_example` — Retrieve example artifacts
- `search_docs` — Search documentation
- `resolve_term` — Resolve glossary terms
- `get_contract_map` — Retrieve contract responsibility map

### 3. Authority & Versioning
- Every response includes contract acronym, version, and status
- Responses indicate authoritative source (`$id` or path)
- Unknown data returns `not_found`, never synthesized content

### 4. Content Scope
OMP servers expose:
- Standards documentation (README, SPECIFICATION, etc.)
- JSON Schemas
- Examples
- Governance metadata
- Glossaries and contract maps

OMP servers do NOT expose:
- Runtime behavior
- Implementation internals
- Business logic
- Private or mutable state

---

## Use Cases

### For LLMs
- Retrieve OKHAM specifications during code generation
- Validate generated artifacts against schemas
- Resolve ambiguous terminology
- Understand contract boundaries and responsibilities

### For Tooling
- Build OKHAM-aware IDEs and linters
- Generate documentation from authoritative sources
- Validate compliance programmatically
- Discover available standards and versions

### For Implementers
- Query standards during implementation
- Retrieve examples for reference
- Understand minimal compliance requirements
- Explore contract relationships

---

## Positioning in OKHAM

OMP is a **supporting integration standard**, not a core contract.

| Category | Examples | Purpose |
|----------|----------|---------|
| **Core Contracts** | OTC, OEC, OPC, OUCC | Define system meaning |
| **Extension Standards** | OBC, OAC | Extend core for specific domains |
| **Tooling & Format Standards** | OCD, OCC, ODT, OSR, OVR/OLR | Enable ecosystem interoperability |
| **Supporting Integration Standards** | **OMP** | Enable machine consumption of standards |

OMP enables **implementation-grade clarity** for LLMs and tooling without defining system semantics.

---

## Getting Started

1. **Read the [SPECIFICATION](SPECIFICATION.md)** for normative requirements
2. **Review the [schema](schema/omp/0.1.0/responses.schema.json)** for response structures
3. **Explore [examples](examples/)** for typical usage patterns

---

## Status

OMP is currently in **DRAFT** status. It will reach **STABLE** alongside OKHAM Core standards.

---

## Related Standards

- [OCD — Open Contract Document](../standards/ocd/README.md) — Meta-format for all OKHAM specs
- [OCC — Open Capability Contract](../standards/occ/README.md) — Capability packaging and discovery
- [MCP Specification](https://modelcontextprotocol.io/) — Model Context Protocol (external)

---

## Navigation

- [← Back to Standards](../standards/README.md)
- [SPECIFICATION →](SPECIFICATION.md)
- [Schema →](schema/omp/0.1.0/responses.schema.json)
- [Examples →](examples/)
