# OKHAM OCD — Open Contract Document
- Contract: OCD
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/ocd/0.1.0/schema.json
- Last Updated: 2026-01-16

---

## 1. Purpose and Scope

### 1.1 Purpose

The Open Contract Document (OCD) specification defines the canonical structure for all OKHAM contract documents. OCD provides a uniform container format that separates normative requirements from non-normative implementations, profiles, and reference material.

### 1.2 Scope

OCD specifies:

- Document structure and section organization
- Normative vs non-normative semantics
- Profile identification and versioning
- Implementation embedding rules
- Compatibility guarantees
- Tooling requirements

OCD does NOT specify:

- Domain-specific semantics (capabilities, types, interactions, UI)
- Programming language implementations
- Runtime behavior
- Storage formats
- Transport protocols

### 1.3 Conformance

A document conforms to OCD if:

1. It includes all required sections as defined in §3
2. It uses RFC 2119 keywords correctly as defined in §4
3. It clearly delineates normative from non-normative content
4. It follows versioning rules as defined in §6

---

## 2. Core Principles

### 2.1 Separation of Concerns

Normative contract definitions MUST be separate from implementations, profiles, and reference materials.

### 2.2 Implementation Independence

Non-normative sections (profiles, language bindings, examples) MUST NOT introduce normative requirements.

### 2.3 Explicit Classification

Every section MUST be explicitly classified as normative or non-normative.

### 2.4 Deterministic Interpretation

Tooling MUST be able to extract normative requirements without human interpretation of prose.

---

## 3. Document Structure

### 3.1 Required Sections

An OCD-compliant document MUST include the following sections in order:

| Section | Classification | Required |
|---------|----------------|----------|
| Header | Informational | YES |
| Purpose and Scope | Normative | YES |
| Normative Content | Normative | YES |
| Profiles and Implementations | Non-Normative | NO |
| Compatibility | Normative | YES |
| Security Considerations | Normative | YES (if applicable) |
| References | Informational | NO |
| Examples | Non-Normative | NO |

### 3.2 Header

The header MUST include:

```yaml
# {Standard Name} — {Expanded Name}

**Version**: {semantic-version}
**Status**: {DRAFT | STABLE | DEPRECATED}
**Last Updated**: {ISO-8601-date}
```

The header MAY include:

- `**Replaces**:` — superseded version
- `**Audience**:` — intended readers
- `**Dependencies**:` — required standards

### 3.3 Purpose and Scope

This section MUST define:

- What the standard specifies (in scope)
- What the standard does NOT specify (out of scope)
- Conformance criteria

### 3.4 Normative Content

This section contains the binding specification. It MUST:

- Use RFC 2119 keywords (MUST, SHOULD, MAY)
- Define structural requirements
- Define semantic requirements
- Define validation rules

Subsections MAY be organized by domain concern.

### 3.5 Profiles and Implementations

This section is **non-normative** and contains:

- Language-specific implementations
- Runtime-specific adaptations
- Reference implementations
- Tooling bindings

Each profile MUST be identified as specified in §5.

### 3.6 Compatibility

This section MUST define:

- Backward compatibility guarantees
- Version migration paths
- Deprecation policies

### 3.7 Security Considerations

If the standard has security implications, this section MUST:

- Identify threat surfaces
- Define security requirements
- Specify validation requirements

If the standard has no security implications, this section MAY be omitted with an explicit statement: "This standard has no direct security considerations."

### 3.8 References

This section lists external dependencies:

- Normative references (required for implementation)
- Informative references (supplementary material)

### 3.9 Examples

This section provides illustrative examples. Examples are **non-normative** unless explicitly stated otherwise.

---

## 4. Normative vs Non-Normative Semantics

### 4.1 RFC 2119 Keywords

OCD adopts RFC 2119 keywords with the following scope:

| Keyword | Meaning | Scope |
|---------|---------|-------|
| **MUST** | Absolute requirement | Normative sections only |
| **MUST NOT** | Absolute prohibition | Normative sections only |
| **REQUIRED** | Synonym for MUST | Normative sections only |
| **SHALL** | Synonym for MUST | Normative sections only |
| **SHALL NOT** | Synonym for MUST NOT | Normative sections only |
| **SHOULD** | Strong recommendation | Normative sections only |
| **SHOULD NOT** | Strong discouragement | Normative sections only |
| **RECOMMENDED** | Synonym for SHOULD | Normative sections only |
| **MAY** | Optional | Normative sections only |
| **OPTIONAL** | Synonym for MAY | Normative sections only |

### 4.2 Normative Section Identification

Normative sections MUST be explicitly identified using one of:

1. Section heading suffix: `(Normative)`
2. Table metadata: `Classification: Normative`
3. Prose statement: "This section is normative."

If not explicitly marked, a section's classification is determined by its position in the document structure (§3.1).

### 4.3 Non-Normative Section Identification

Non-normative sections MUST be explicitly identified using:

1. Section heading prefix: `(Non-Normative)`
2. Prose statement: "This section is non-normative."

### 4.4 Keyword Scope

RFC 2119 keywords appearing in non-normative sections have **no normative force**. They serve only as guidance for implementers.

---

## 5. Profiles and Implementations

### 5.1 Purpose

Profiles allow language-specific, runtime-specific, or platform-specific adaptations of the normative specification without modifying the core standard.

### 5.2 Profile Structure

Each profile MUST include:

```yaml
## Profile: {Profile-Name}

**Type**: {language | runtime | platform}
**Target**: {Go | TypeScript | Python | etc.}
**Version**: {semantic-version}
**Status**: {DRAFT | STABLE | DEPRECATED}
**Maintainer**: {name-or-organization}
```

### 5.3 Profile Identification

Profiles MUST use reverse-DNS naming:

```
{domain}.{standard}.{target}.{aspect}
```

**Examples**:

- `okham.occ.go.types` — Go type definitions for OCC
- `okham.otc.typescript.validators` — TypeScript validators for OTC

### 5.4 Profile Versioning

Profiles are versioned **independently** from the normative specification.

A profile MAY specify compatibility:

```yaml
**Compatible With**: {standard-version-range}
```

**Example**:

```yaml
**Compatible With**: OCC 1.0.x - 1.2.x
```

### 5.5 Profile Optionality

Profiles are **always non-normative**. Implementations:

- MUST NOT be required to use a specific profile
- MUST conform to the normative specification
- MAY use profiles as reference or convenience

### 5.6 Profile Content

Profiles MAY contain:

- Code snippets
- Library references
- Implementation notes
- Performance characteristics
- Best practices

Profiles MUST NOT:

- Introduce new normative requirements
- Contradict the normative specification
- Claim to be the only valid implementation

---

## 6. Versioning and Compatibility

### 6.1 Semantic Versioning

OCD-compliant documents MUST use semantic versioning (semver 2.0.0):

```
MAJOR.MINOR.PATCH
```

**Version semantics**:

- `MAJOR`: Breaking changes to normative requirements
- `MINOR`: Backward-compatible additions
- `PATCH`: Non-breaking clarifications, corrections, or non-normative updates

### 6.2 Version Compatibility

A document with version `X.Y.Z`:

- MUST be compatible with `X.Y.*` (same major/minor)
- SHOULD be forward-compatible with `X.(Y+1).*` (next minor)
- MAY break compatibility with `(X+1).*.*` (next major)

### 6.3 Deprecation

Deprecated features MUST:

1. Be marked with `**DEPRECATED**` in the section heading
2. Specify a replacement (if applicable)
3. Specify a removal timeline (if known)

**Example**:

```markdown
### 4.3 Legacy Format (**DEPRECATED**)

**Deprecated in**: 2.1.0
**Removed in**: 3.0.0
**Replacement**: See §4.5
```

### 6.4 Status Lifecycle

Documents follow this lifecycle:

| Status | Meaning | Stability |
|--------|---------|-----------|
| **DRAFT** | Under development, subject to change | Unstable |
| **CANDIDATE** | Stabilizing, implementation feedback invited | Maturing |
| **STABLE** | Production-ready, compatibility guaranteed | Stable |
| **DEPRECATED** | Superseded, use replacement | Legacy |

---

## 7. Tooling Expectations

### 7.1 Machine Readability

Tooling MUST be able to:

1. Extract version metadata from the header
2. Identify normative vs non-normative sections
3. Parse RFC 2119 keywords in normative sections
4. List available profiles

### 7.2 Validation

Tooling SHOULD:

- Validate document structure (§3)
- Verify RFC 2119 keyword usage
- Check version format (§6.1)
- Detect normative/non-normative boundary violations

### 7.3 Documentation Generation

Tooling MAY:

- Generate navigable HTML from OCD documents
- Generate per-profile documentation
- Generate API references from profiles

### 7.4 Metadata Format

OCD does not mandate a specific metadata format (YAML, JSON, TOML), but recommends YAML for human readability.

---

## 8. Minimal Example

### 8.1 Generic Contract Document

```markdown
# XYZ — Example Contract

**Version**: 1.0.0
**Status**: DRAFT
**Last Updated**: 2026-01-16
**Audience**: Implementers, Tooling Authors

---

## 1. Purpose and Scope

### 1.1 Purpose

The Example Contract (XYZ) defines a canonical structure for illustrative purposes.

### 1.2 Scope

XYZ specifies:

- A minimal normative structure
- Example profiles

XYZ does NOT specify:

- Production semantics
- Real-world implementations

### 1.3 Conformance

An implementation conforms to XYZ if it satisfies all normative requirements in §2.

---

## 2. Normative Specification (Normative)

### 2.1 Core Requirements

An XYZ document MUST include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `version` | string | YES | XYZ version (semver) |
| `id` | string | YES | Unique identifier |
| `data` | object | YES | Payload |

### 2.2 Identifier Format

The `id` field MUST:

- Be a valid UUID v4
- Be globally unique

### 2.3 Data Structure

The `data` field MUST be a JSON object.

---

## 3. Profiles and Implementations (Non-Normative)

### 3.1 Profile: Go Types

**Type**: language
**Target**: Go
**Version**: 1.0.0
**Status**: STABLE
**Maintainer**: OKHAM Contributors
**Compatible With**: XYZ 1.0.x

```go
package xyz

type Document struct {
    Version string                 `json:"version"`
    ID      string                 `json:"id"`
    Data    map[string]interface{} `json:"data"`
}
```

### 3.2 Profile: TypeScript Types

**Type**: language
**Target**: TypeScript
**Version**: 1.0.0
**Status**: STABLE
**Maintainer**: OKHAM Contributors
**Compatible With**: XYZ 1.0.x

```typescript
interface XYZDocument {
  version: string;
  id: string;
  data: Record<string, unknown>;
}
```

---

## 4. Compatibility (Normative)

### 4.1 Version Compatibility

XYZ 1.x implementations MUST accept all valid XYZ 1.y documents where `y >= x`.

### 4.2 Forward Compatibility

XYZ implementations SHOULD ignore unknown fields to enable forward compatibility.

---

## 5. Security Considerations (Normative)

### 5.1 Identifier Collision

Implementations MUST ensure `id` uniqueness to prevent collision attacks.

### 5.2 Data Validation

Implementations MUST validate `data` structure before processing to prevent injection attacks.

---

## 6. Examples (Non-Normative)

### 6.1 Minimal Document

```json
{
  "version": "1.0.0",
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "data": {
    "example": "value"
  }
}
```

---

## 7. References

### 7.1 Normative References

- RFC 2119: Key words for use in RFCs to Indicate Requirement Levels
- RFC 4122: A Universally Unique Identifier (UUID) URN Namespace

### 7.2 Informative References

- Semantic Versioning 2.0.0: https://semver.org/
```

---

## 9. Compliance

### 9.1 OCD Self-Compliance

This document (OCD 1.0.0) is itself OCD-compliant.

### 9.2 Contract Adoption

All OKHAM contracts (OCC, OTC, OIC, OUCC, OHB, OSC) MUST adopt OCD structure starting with their next major version.

### 9.3 Transition Period

Existing contracts MAY reference OCD compliance without restructuring until their next major version release.

---

## 10. References

### 10.1 Normative References

- RFC 2119: Key words for use in RFCs to Indicate Requirement Levels
- Semantic Versioning 2.0.0: https://semver.org/

### 10.2 Informative References

- W3C Standards Process: https://www.w3.org/standards/

---

## Status

This standard is currently in **DRAFT** status.

Changes may occur based on implementation experience and community feedback.
