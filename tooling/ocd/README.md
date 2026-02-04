# OCD — Open Contract Document

**Version**: 1.0.0
**Status**: DRAFT
**Audience**: Standards Authors, Contract Implementers, Tooling Developers

---

## What Problem Does This Solve?

OKHAM has multiple contract standards (OIC, OTC, OCC, OUCC, OHB, OSC, CSIL), each defining different aspects of the ecosystem. Without a unified document structure:

- **Normative requirements** get mixed with implementation examples
- **Language-specific profiles** claim to be mandatory
- **Tooling cannot reliably** extract requirements from prose
- **Implementations evolve coupled** to specifications
- **Standards are inconsistently structured**, making them hard to navigate

This creates confusion about what is binding (normative) versus what is guidance (non-normative).

## Why Does This Problem Exist?

Most standards evolve organically:
- READMEs accumulate implementation details
- SPECIFICATIONs embed code snippets
- Language bindings appear in normative sections
- Profiles become de-facto requirements

Without explicit separation, the line between "what you MUST do" and "how one implementation does it" blurs. This makes standards:
- Hard to implement in new languages
- Difficult for tooling to validate
- Prone to accidental coupling

## What Happens Without This Standard?

Without OCD:

- Every OKHAM standard uses a different document structure
- Implementers cannot tell what parts are binding
- Tooling must parse prose to extract requirements
- Language profiles accidentally introduce new requirements
- Standards cannot evolve independently of implementations
- RFC 2119 keywords (MUST, SHOULD, MAY) appear in non-normative sections, causing confusion

## What Benefits Does This Enable?

OCD enables:

1. **Consistent Structure**: All OKHAM standards use the same section ordering
2. **Clear Separation**: Normative requirements are distinct from implementation profiles
3. **Implementation Independence**: Go, TypeScript, Python profiles evolve separately from specs
4. **Tooling Support**: Automated validation of standard structure
5. **Accidental Coupling Prevention**: Non-normative sections cannot introduce requirements

## Who Is This For?

**Standards Authors**: Template for writing new OKHAM contracts
**Contract Implementers**: Clear understanding of what is required vs optional
**Tooling Developers**: Predictable document structure for parsing and validation
**System Architects**: Understanding the meta-structure of OKHAM standards

---

## What This Standard Defines

OCD defines:

- **Required sections** for all contract documents
- **Normative vs non-normative semantics** (RFC 2119 scope)
- **Profile identification and versioning**
- **Implementation embedding rules**
- **Compatibility and versioning semantics**
- **Tooling expectations**

## What This Standard Does NOT Define

OCD explicitly does NOT define:

- **Domain-specific semantics** (capabilities, types, interactions)
- **Programming language implementations** (those belong in profiles)
- **Runtime behavior** (execution is outside OCD scope)
- **Storage formats** (contracts may be YAML, JSON, TOML)
- **Content of standards** (only structure and organization)

---

## When to Use OCD

Use OCD when:

- Writing a new OKHAM standard
- Restructuring an existing standard for clarity
- Ensuring a standard is implementation-independent
- Building tooling that validates standard structure

## When NOT to Use OCD

Do NOT use OCD when:

- Writing implementation-specific documentation (use profiles)
- Creating tutorials or guides (non-normative content)
- Defining runtime behavior (OCD is about document structure only)

OCD is a meta-standard. It describes how to structure standards, not what they contain.

---

## Relationship to Other Standards

### Foundation for All Standards

OCD is the **structural template** for:
- **OIC** — Open Interaction Contract
- **OTC** — Open Type Contract
- **OCC** — Open Capability Contract
- **OUCC** — Open UI Component Contract
- **OHB** — Open Hook Bindings
- **CSIL** — Capability Self-Improvement Loop
- **OSC** — Operational Semantic Context

### Self-Compliance

OCD itself follows OCD structure, demonstrating its applicability.

### Explicit Non-Overlap

OCD does NOT define:
- **Interaction semantics**: Defined by OIC
- **Type systems**: Defined by OTC
- **Capability contracts**: Defined by OCC
- **UI structure**: Defined by OUCC
- **Hook bindings**: Defined by OHB

OCD only defines the **container format** for these standards.

---

## Quick Example

An OCD-compliant standard looks like:

```markdown
# XYZ — Example Standard

**Version**: 1.0.0
**Status**: DRAFT

## 1. Purpose and Scope
[What this standard defines and does NOT define]

## 2. Normative Content
[MUST/SHOULD/MAY requirements]

## 3. Profiles and Implementations (Non-Normative)
### Profile: Go Types
[Language-specific bindings]

## 4. Compatibility
[Versioning rules]

## 5. Examples (Non-Normative)
[Illustrative examples]
```

**Key principle**: Normative requirements are separate from non-normative profiles.

---

## Documents

| Document | Purpose |
|----------|---------|
| [SPECIFICATION](SPECIFICATION.md) | Normative definition of OCD structure |

**Note**: OCD does not require separate SCHEMA or EXAMPLES files since the SPECIFICATION includes illustrative examples as part of its definition.

---

## Status

This standard is currently in **DRAFT** status.

All OKHAM contracts MUST adopt OCD structure starting with their next major version.
