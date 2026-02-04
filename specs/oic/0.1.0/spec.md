# OKHAM OIC — Open Intent Contract
- Contract: OIC
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/oic/0.1.0/schema.json
- Date: 2026-01-19

---

## 1. Canonical Definition

### 1.1 What OIC Is

OIC defines a **declarative contract** for intent in AI-native systems. It preserves the purpose, expectations, constraints, and allowed transitions of reasoning and action across agents, models, and runtimes.

### 1.2 What OIC Is Not

OIC is explicitly **not**:
- A runtime protocol or transport layer
- A prompt format or instruction language
- An execution framework or tooling interface
- A model architecture standard

---

## 2. Intent Envelope

An intent envelope MUST include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `oic` | string | YES | Contract version (semver) |
| `id` | string | YES | Unique intent declaration identifier |
| `intent` | string | YES | Intent token from the Intent Registry |
| `purpose` | string | YES | Human-readable purpose statement |
| `constraints` | object | NO | Declarative constraints on the intent |
| `expectations` | object | NO | Expected outcome description |
| `transitions` | array | NO | Allowed next intents |

Intent envelopes MUST be immutable once issued.

---

## 3. Intent Registry v0.1

The following intents are canonical and MUST NOT be redefined:

| Intent | Semantics | Execution Allowed |
|--------|-----------|------------------|
| `analyze` | Understand, classify, diagnose | No |
| `propose` | Generate options or alternatives | No |
| `decide` | Choose among proposals with criteria | No |
| `plan` | Step-by-step plan with dependencies | No |
| `execute` | Produce effects (tools/code/changes) | Requires policy allowance and prior decide/plan |
| `verify` | Validate outcomes against expectations | No |
| `refine` | Incremental improvements without changing objectives | No |
| `review` | Independent critique or quality assessment | No |
| `explain` | Communicate an already-decided or executed result | No |

---

## 4. Transitions

Transitions MUST be explicit. If `transitions` are present, each entry MUST be an intent token from the registry.

Implementations MUST NOT move to `execute` without a preceding `decide` or `plan` intent unless an external policy explicitly allows it.

---

## 5. Relationships to Other Standards

OIC defines intent. OPC governs intent. OCC executes intent.

OIC integrates with:
- **OPC** for policy constraints on intent transitions
- **OCC** for execution delegation
- **OUCC** for intent-aware UI flows

---

## 6. Minimal Schema Examples

### 6.1 Intent Envelope (YAML)

```yaml
oic: "0.1.0"
id: "intent-001"
intent: "plan"
purpose: "Outline steps to resolve a customer ticket"
constraints:
  policy: "helpdesk.close-permission"
expectations:
  outcome: "Plan includes verification step"
transitions:
  - "execute"
  - "verify"
```

### 6.2 Intent Registry Block (YAML)

```yaml
registry: "oic.intent.v0.1"
intents:
  - id: "analyze"
    description: "Understand, classify, diagnose; no proposals or execution"
  - id: "execute"
    description: "Produce effects; requires policy allowance and prior decide/plan"
```

---

## 7. Versioning and Compatibility

OIC uses semantic versioning:
- **MAJOR** changes break intent semantics or required fields
- **MINOR** changes add optional fields or intent metadata
- **PATCH** changes clarify wording without semantic impact

Consumers MUST reject incompatible MAJOR versions and SHOULD accept newer MINOR versions.

---

## 8. References

- [OCD — Open Contract Document](../ocd/SPECIFICATION.md)
- [OPC — Open Policy Contract](../opc/README.md)
- [OCC — Open Capability Contract](../occ/README.md)

---

**End of Specification**
