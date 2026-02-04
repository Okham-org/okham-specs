# OKHAM OMP — OKHAM MCP Profile
- Contract: OMP
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/omp/0.1.0/schema.json
- Last Updated: 2026-01-16

---

## Abstract

This document specifies **OMP (OKHAM MCP Profile)**, a supporting integration standard that defines how OKHAM standards are exposed through the Model Context Protocol (MCP). OMP is a **profile** of MCP, not a replacement or extension. It specifies required MCP tools, response structures, authority guarantees, and determinism requirements for OKHAM-compliant MCP servers.

---

## Conformance

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

---

## 1. Profile Identification

### 1.1 Profile Metadata

An OMP-compliant MCP server MUST expose the following metadata:

- **Profile Name**: `okham-mcp-profile`
- **Profile Version**: Semantic version (e.g., `0.1.0`)
- **OMP Specification Version**: Version of this specification implemented

### 1.2 Discovery

An OMP server MUST respond to MCP's standard capability discovery with:

```json
{
  "capabilities": {
    "tools": {},
    "resources": {},
    "prompts": {}
  },
  "serverInfo": {
    "name": "okham-standards-server",
    "version": "1.0.0"
  },
  "protocolVersion": "2024-11-05",
  "extensions": {
    "omp": {
      "version": "0.1.0",
      "profile": "okham-mcp-profile"
    }
  }
}
```

The `extensions.omp` field MUST be present and MUST include `version` and `profile`.

---

## 2. Required MCP Tools

An OMP-compliant server MUST implement the following MCP tools. Each tool is defined using MCP's standard tool schema.

### 2.1 `list_contracts`

**Purpose**: List all OKHAM contracts available on this server.

**Input Parameters**: None

**Output Structure**:

```json
{
  "contracts": [
    {
      "acronym": "OTC",
      "name": "Open Type Contract",
      "version": "0.1.0",
      "status": "DRAFT",
      "category": "core",
      "path": "/standards/otc/"
    }
  ],
  "metadata": {
    "omp_version": "0.1.0",
    "timestamp": "2026-01-16T12:00:00Z"
  }
}
```

**Requirements**:
- The `contracts` array MUST include all contracts the server exposes
- Each contract MUST include `acronym`, `name`, `version`, `status`, `category`, and `path`
- `status` MUST be one of: `DRAFT`, `STABLE`, `COMMUNITY-GOVERNED`, `DEPRECATED`
- `category` MUST be one of: `core`, `extension`, `tooling`, `supporting`

### 2.2 `get_contract_doc`

**Purpose**: Retrieve documentation for a specific contract.

**Input Parameters**:

```json
{
  "contract": "OTC",
  "document": "SPECIFICATION",
  "version": "0.1.0"
}
```

- `contract` (REQUIRED): Contract acronym (e.g., `OTC`)
- `document` (REQUIRED): Document type (`README`, `SPECIFICATION`, `SCHEMA`, `EXAMPLES`, `GOVERNANCE`)
- `version` (OPTIONAL): Specific version. If omitted, returns latest.

**Output Structure**:

```json
{
  "contract": "OTC",
  "document": "SPECIFICATION",
  "version": "0.1.0",
  "status": "DRAFT",
  "content": "# OTC Specification\n\n...",
  "format": "markdown",
  "metadata": {
    "path": "/standards/otc/SPECIFICATION.md",
    "last_updated": "2026-01-15T10:00:00Z",
    "authoritative_source": "https://okham.org/standards/otc/SPECIFICATION.html"
  }
}
```

**Requirements**:
- If the contract or document does not exist, the server MUST return an error with `code: "not_found"`
- The server MUST NOT synthesize or infer content
- `content` MUST be the exact, authoritative content
- `format` MUST be one of: `markdown`, `json`, `yaml`, `html`

### 2.3 `get_schema`

**Purpose**: Retrieve JSON Schema for a contract.

**Input Parameters**:

```json
{
  "contract": "OTC",
  "version": "0.1.0"
}
```

- `contract` (REQUIRED): Contract acronym
- `version` (OPTIONAL): Specific version. If omitted, returns latest.

**Output Structure**:

```json
{
  "contract": "OTC",
  "version": "0.1.0",
  "schema": {
    "$id": "https://okham.org/schema/otc/0.1.0/type.schema.json",
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "type": "object",
    "properties": {}
  },
  "metadata": {
    "schema_id": "https://okham.org/schema/otc/0.1.0/type.schema.json",
    "last_updated": "2026-01-15T10:00:00Z"
  }
}
```

**Requirements**:
- The `schema` field MUST contain a valid JSON Schema
- The schema MUST include a `$id` field with the authoritative schema URI
- If no schema exists for the contract, the server MUST return `code: "not_found"`

### 2.4 `get_example`

**Purpose**: Retrieve example artifacts for a contract.

**Input Parameters**:

```json
{
  "contract": "OTC",
  "example_id": "basic-type",
  "version": "0.1.0"
}
```

- `contract` (REQUIRED): Contract acronym
- `example_id` (OPTIONAL): Specific example identifier. If omitted, returns all examples.
- `version` (OPTIONAL): Specific version. If omitted, returns latest.

**Output Structure**:

```json
{
  "contract": "OTC",
  "version": "0.1.0",
  "examples": [
    {
      "id": "basic-type",
      "title": "Basic Type Definition",
      "description": "A simple type with semantic metadata",
      "artifact": {},
      "validates_against": "https://okham.org/schema/otc/0.1.0/type.schema.json"
    }
  ],
  "metadata": {
    "path": "/standards/otc/EXAMPLES.md",
    "last_updated": "2026-01-15T10:00:00Z"
  }
}
```

**Requirements**:
- Each example MUST include `id`, `title`, `artifact`, and `validates_against`
- The `validates_against` field MUST reference the schema URI the example is valid against
- Examples MUST be schema-valid (servers SHOULD validate before serving)

### 2.5 `search_docs`

**Purpose**: Search OKHAM documentation.

**Input Parameters**:

```json
{
  "query": "event causality",
  "contracts": ["OEC", "OTC"],
  "document_types": ["SPECIFICATION"]
}
```

- `query` (REQUIRED): Search query string
- `contracts` (OPTIONAL): Filter by contract acronyms
- `document_types` (OPTIONAL): Filter by document types

**Output Structure**:

```json
{
  "results": [
    {
      "contract": "OEC",
      "document": "SPECIFICATION",
      "version": "0.1.0",
      "excerpt": "...causality chains enable event correlation...",
      "relevance_score": 0.95,
      "path": "/standards/oec/SPECIFICATION.md#causality"
    }
  ],
  "metadata": {
    "query": "event causality",
    "total_results": 1,
    "timestamp": "2026-01-16T12:00:00Z"
  }
}
```

**Requirements**:
- Results MUST be ordered by relevance (highest first)
- `excerpt` SHOULD include context around the match
- If no results are found, return an empty `results` array (not an error)

### 2.6 `resolve_term`

**Purpose**: Resolve a term from the OKHAM glossary.

**Input Parameters**:

```json
{
  "term": "capability"
}
```

- `term` (REQUIRED): Term to resolve

**Output Structure**:

```json
{
  "term": "capability",
  "definition": "A discrete unit of functionality that can be invoked, composed, and governed.",
  "related_contracts": ["OCC"],
  "aliases": ["cap"],
  "metadata": {
    "source": "/foundation/GLOSSARY.md",
    "last_updated": "2026-01-15T10:00:00Z"
  }
}
```

**Requirements**:
- If the term does not exist, return `code: "not_found"`
- The server MUST NOT infer or synthesize definitions
- `related_contracts` SHOULD list contracts where the term is normatively defined

### 2.7 `get_contract_map`

**Purpose**: Retrieve the contract responsibility map.

**Input Parameters**: None

**Output Structure**:

```json
{
  "contracts": [
    {
      "acronym": "OTC",
      "owns": ["Type definitions", "Semantic metadata", "Type composition"],
      "does_not_own": ["Runtime type checking", "Serialization format", "Validation logic"],
      "depends_on": []
    },
    {
      "acronym": "OEC",
      "owns": ["Event structure", "Causality chains", "Event classification"],
      "does_not_own": ["Event transport", "Event storage", "Event processing logic"],
      "depends_on": ["OTC"]
    }
  ],
  "metadata": {
    "source": "/foundation/CONTRACT-MAP.md",
    "last_updated": "2026-01-15T10:00:00Z"
  }
}
```

**Requirements**:
- Each contract MUST include `acronym`, `owns`, `does_not_own`, and `depends_on`
- The map MUST be consistent with the authoritative CONTRACT-MAP.md document

---

## 3. Response Structure

### 3.1 Success Response

All successful tool responses MUST include:

```json
{
  "content": [
    {
      "type": "text",
      "text": "<JSON response as string>"
    }
  ]
}
```

The JSON response string MUST be parseable and MUST conform to the tool's output structure.

### 3.2 Error Response

All error responses MUST use MCP's standard error format:

```json
{
  "error": {
    "code": "not_found",
    "message": "Contract 'XYZ' does not exist"
  }
}
```

**Standard Error Codes**:
- `not_found` — Requested resource does not exist
- `invalid_params` — Invalid input parameters
- `internal_error` — Server-side error
- `unsupported_version` — Requested version not available

### 3.3 Metadata Requirements

All responses MUST include a `metadata` object with:
- `omp_version` or equivalent version identifier
- `timestamp` (ISO 8601 format)

Responses SHOULD include:
- `path` — Authoritative source path
- `last_updated` — Last modification timestamp

---

## 4. Authority & Versioning

### 4.1 Authoritative Source

Every response MUST indicate the authoritative source of the data:
- `$id` field in schemas
- `path` or `authoritative_source` in metadata

### 4.2 Version Handling

- If a specific version is requested and does not exist, return `code: "not_found"`
- If no version is specified, return the latest STABLE version
- If no STABLE version exists, return the latest DRAFT version
- Version strings MUST follow semantic versioning (MAJOR.MINOR.PATCH)

### 4.3 Status Indicators

Every contract reference MUST include a `status` field:
- `DRAFT` — Active development, breaking changes expected
- `STABLE` — Production-ready, semantic versioning enforced
- `COMMUNITY-GOVERNED` — Open governance model
- `DEPRECATED` — No longer recommended, migration path available

### 4.4 No Synthesis Rule

**Critical Requirement**: An OMP server MUST NOT:
- Synthesize content not present in authoritative sources
- Infer semantics or relationships
- Generate examples not explicitly provided
- Hallucinate definitions or specifications

If data does not exist, the server MUST return `not_found`.

---

## 5. Content Scope

### 5.1 Exposed Content

OMP servers MUST expose:
- Standards documentation (README, SPECIFICATION, SCHEMA, EXAMPLES, GOVERNANCE)
- JSON Schemas with `$id` URIs
- Validated examples
- Glossary terms
- Contract maps
- Governance metadata

### 5.2 Prohibited Content

OMP servers MUST NOT expose:
- Runtime behavior or implementation details
- Business logic or proprietary algorithms
- Private or mutable state
- User data or session information
- Tooling internals (linters, validators, etc.)

---

## 6. Transport & Security

### 6.1 Read-Only Behavior

OMP servers MUST be **read-only**. They MUST NOT:
- Accept write operations
- Modify standards content
- Store user state
- Execute arbitrary code

### 6.2 Authentication

For public OKHAM standards:
- Authentication SHOULD NOT be required
- Servers MAY implement rate limiting

For private or enterprise standards:
- Authentication MAY be required
- Authentication mechanisms are implementation-specific

### 6.3 CORS

OMP servers SHOULD support CORS to enable browser-based tooling:
- `Access-Control-Allow-Origin: *` for public standards
- Appropriate CORS headers for authenticated access

### 6.4 Cache Control

Responses SHOULD include cache control headers:
- `Cache-Control: public, max-age=3600` for STABLE versions
- `Cache-Control: no-cache` for DRAFT versions

---

## 7. Endpoint Conventions

### 7.1 Base Endpoint

OMP servers SHOULD use the following endpoint convention:

- **HTTP/HTTPS**: `https://okham.org/mcp/`
- **Stdio**: Standard MCP stdio transport

### 7.2 Health Endpoint

OMP servers SHOULD expose a health endpoint:

```
GET /mcp/health
```

Response:

```json
{
  "status": "healthy",
  "omp_version": "0.1.0",
  "timestamp": "2026-01-16T12:00:00Z"
}
```

---

## 8. Conformance Levels

### 8.1 Minimal Conformance

A minimally conformant OMP server MUST:
- Implement all 7 required tools
- Return correct response structures
- Include version and status metadata
- Never synthesize content

### 8.2 Full Conformance

A fully conformant OMP server MUST:
- Meet minimal conformance requirements
- Implement health endpoint
- Support CORS
- Include cache control headers
- Validate examples before serving

---

## 9. Implementation Notes (Non-Normative)

### 9.1 Typical Pitfalls

**Pitfall 1: Synthesizing Content**

Servers may be tempted to generate examples or infer relationships. This violates the no-synthesis rule.

**Solution**: Only serve content explicitly present in authoritative sources.

**Pitfall 2: Version Confusion**

Mixing versions or returning outdated content can break implementations.

**Solution**: Always include version metadata and validate version requests.

**Pitfall 3: Overly Broad Search**

Search results that include non-authoritative content (blog posts, forums) dilute authority.

**Solution**: Only search within official OKHAM documentation.

### 9.2 Minimal Viable Implementation

A minimal OMP server can be implemented as:
1. A static file server with JSON wrappers
2. A simple API that reads from a docs directory
3. A Cloudflare Worker that proxies to GitHub

No complex infrastructure is required.

### 9.3 Example Resolution Flow

```
LLM: "What fields are required in an OTC type?"
→ Tool: get_contract_doc(contract="OTC", document="SPECIFICATION")
→ Server: Returns SPECIFICATION.md content
→ LLM: Parses specification, extracts required fields
→ LLM: Generates code with correct structure
```

---

## 10. Relationship to OKHAM Core

OMP is a **supporting integration standard**. It does NOT:
- Define system semantics (that's OTC, OEC, OPC, OUCC)
- Replace MCP (it profiles MCP)
- Mandate implementations (it enables them)

OMP exists to make OKHAM standards **machine-consumable** without redefining protocols or semantics.

---

## 11. References

### Normative References

- [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) — Key words for use in RFCs
- [Model Context Protocol](https://modelcontextprotocol.io/) — MCP Specification
- [JSON Schema](https://json-schema.org/) — JSON Schema Specification

### Informative References

- [OCD — Open Contract Document](../standards/ocd/SPECIFICATION.md)
- [OKHAM Foundation Documentation](../foundation/README.md)

---

## 12. Changelog

### Version 0.1.0 (2026-01-16)

- Initial DRAFT specification
- Defined 7 required MCP tools
- Specified response structures and error handling
- Established authority and versioning requirements
- Defined content scope and security constraints

---

## Appendix A: Complete Tool Schema (Non-Normative)

See [schema/omp/0.1.0/responses.schema.json](schema/omp/0.1.0/responses.schema.json) for machine-readable tool schemas.

---

## Appendix B: Example Server Implementation (Non-Normative)

A reference implementation can be found at:
- GitHub: `okham-org/omp-reference-server`
- Cloudflare Worker: `https://okham.org/mcp/`

---

**End of Specification**
