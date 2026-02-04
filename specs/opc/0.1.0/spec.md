# OKHAM OPC — Open Policy Contract
- Contract: OPC
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/opc/0.1.0/schema.json
- Last Updated: 2026-01-16

---

## Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Policy Structure](#2-policy-structure)
3. [Policy Identity](#3-policy-identity)
4. [Policy Versioning](#4-policy-versioning)
5. [Condition Expressions](#5-condition-expressions)
6. [Decision Outcomes](#6-decision-outcomes)
7. [Applicability Targets](#7-applicability-targets)
8. [Policy Scope](#8-policy-scope)
9. [Compatibility Rules](#9-compatibility-rules)
10. [Relationship to Other Standards](#10-relationship-to-other-standards)
11. [Conformance](#11-conformance)

---

## 1. Introduction

### 1.1 Purpose

The Open Policy Contract (OPC) defines a **normative contract for declarative policies** that constrain, allow, deny, or condition behavior in a system.

### 1.2 Scope

OPC specifies:

- Policy structure and required fields
- Policy identity and naming conventions
- Policy versioning and compatibility rules
- Condition expression syntax (declarative)
- Decision outcome semantics (allow, deny, constrain, warn)
- Applicability targets (what the policy applies to)
- Policy scope (global, service, capability)

### 1.3 Non-Goals

OPC explicitly **does NOT define**:

- Execution engines (OPA, Cedar, Rego, custom engines)
- Enforcement mechanisms (middleware, interceptors, proxies)
- Business logic (policies define constraints, not application behavior)
- Runtime evaluation order or optimization
- Policy distribution or deployment mechanisms
- Policy conflict resolution strategies (engine-specific)

### 1.4 Policy Semantics

A **policy** is:
- A **declarative constraint** expressed as data
- **Versioned** for auditability and evolution
- **Evaluated** against contextual inputs (from OSC)
- **Engine-independent** (not tied to specific policy frameworks)
- **Immutable once deployed** (changes require new version)

**Policies are NOT**:
- Business rules (application logic)
- Hooks (extension points)
- Commands (requests to execute actions)
- Events (facts about what happened)

---

## 2. Policy Structure

### 2.1 Root Object

Every OPC policy MUST be a structured document containing:

| Field        | Type    | Required | Description                             |
|--------------|---------|----------|-----------------------------------------|
| `policy`     | string  | REQUIRED | OPC version (semantic version)          |
| `id`         | string  | REQUIRED | Policy identifier (unique, namespaced)  |
| `name`       | string  | REQUIRED | Human-readable policy name              |
| `version`    | string  | REQUIRED | Policy version                          |
| `status`     | string  | REQUIRED | Lifecycle status                        |
| `scope`      | string  | REQUIRED | Policy scope (global, service, capability) |
| `target`     | object  | OPTIONAL | Applicability target                    |
| `condition`  | object  | REQUIRED | Policy condition expression             |
| `decision`   | object  | REQUIRED | Decision outcomes                       |
| `metadata`   | object  | OPTIONAL | Additional metadata                     |

### 2.2 Policy Field

The `policy` field MUST:
- Contain the OPC version as a semantic version string
- Match the pattern: `^[0-9]+\.[0-9]+\.[0-9]+$`
- Indicate which version of OPC the policy conforms to

**Example**:
```yaml
policy: 0.1.0
```

---

## 3. Policy Identity

### 3.1 Policy ID

The `id` field MUST:
- Uniquely identify the policy across the system
- Use reverse-domain notation or hierarchical namespacing
- Be lowercase with dot separators
- Match the pattern: `^[a-z][a-z0-9._-]*[a-z0-9]$`
- Be stable across versions (policy ID does not change when version changes)

**Recommended Structure**:
```
<domain>.<category>.<policy-name>
```

**Examples**:
- `auth.access-control.user-data-access`
- `rate-limit.api.requests-per-minute`
- `compliance.data-governance.pii-access`

### 3.2 Policy Name

The `name` field MUST:
- Provide a human-readable description of the policy
- Use Title Case
- Be concise (recommended: 2-6 words)

**Examples**:
- `User Data Access Policy`
- `API Rate Limit Policy`
- `PII Access Compliance Policy`

---

## 4. Policy Versioning

### 4.1 Version Field

The `version` field MUST:
- Follow semantic versioning (MAJOR.MINOR.PATCH)
- Increment MAJOR for breaking changes
- Increment MINOR for backward-compatible additions
- Increment PATCH for clarifications or non-functional changes

### 4.2 Breaking Changes

A policy change is **breaking** if it:
- Changes condition expression semantics (makes previously allowed actions denied)
- Changes decision outcomes (flips allow/deny)
- Removes supported inputs
- Changes the policy scope in a way that affects existing evaluations

### 4.3 Non-Breaking Changes

A policy change is **non-breaking** if it:
- Adds new optional condition inputs
- Relaxes constraints (allows previously denied actions)
- Adds new decision outcomes (e.g., adds `warn` without changing `allow`)
- Adds clarifying documentation

### 4.4 Status Field

The `status` field MUST be one of:
- `draft` — Under development, not enforced
- `active` — Currently enforced in production
- `deprecated` — Superseded, enforcement discouraged
- `removed` — No longer enforced

---

## 5. Condition Expressions

### 5.1 Condition Object

The `condition` field MUST contain:

| Field        | Type   | Required | Description                           |
|--------------|--------|----------|---------------------------------------|
| `expression` | string | REQUIRED | Declarative condition expression      |
| `inputs`     | array  | REQUIRED | List of required input variables      |
| `language`   | string | OPTIONAL | Expression language (default: `cel`)  |

### 5.2 Expression Language

The `expression` field MUST:
- Be a declarative boolean expression
- Evaluate to `true` (condition met) or `false` (condition not met)
- Reference only inputs declared in the `inputs` array
- Not contain imperative logic (no side effects)

**Default Language**: Common Expression Language (CEL)

**Example**:
```yaml
condition:
  expression: "subject.role == 'admin' OR subject.id == resource.owner_id"
  inputs:
    - subject.role
    - subject.id
    - resource.owner_id
  language: cel
```

### 5.3 Input Variables

The `inputs` field MUST:
- List all variables referenced in the `expression`
- Use dot notation for nested properties
- Reference OSC semantic context or capability outputs

**Naming Convention**:
- `subject.*` — Actor performing the action (e.g., `subject.id`, `subject.role`)
- `resource.*` — Target of the action (e.g., `resource.owner_id`, `resource.type`)
- `action.*` — Action being performed (e.g., `action.operation`, `action.method`)
- `environment.*` — Environmental context (e.g., `environment.time`, `environment.ip`)

---

## 6. Decision Outcomes

### 6.1 Decision Object

The `decision` field MUST contain at least one outcome:

Policy engines SHOULD emit a **policy decision payload** when enforcement blocks an action. The canonical payload type is:

- **Type ID**: `okham.policy.decision`
- **Version**: `1.0.0`

Canonical payload shape (OTC fields):
- `policyId` (string)
- `decision` (string: allow|deny|warn|constrain)
- `reasonCode` (string)
- `message` (string)
- `correlationId` (string)
- `target` (string)

| Field       | Type   | Required | Description                          |
|-------------|--------|----------|--------------------------------------|
| `allow`     | object | OPTIONAL | Outcome when condition is true       |
| `deny`      | object | OPTIONAL | Outcome when condition is false      |
| `constrain` | object | OPTIONAL | Conditional constraints              |
| `warn`      | object | OPTIONAL | Warning without blocking             |

### 6.2 Allow Outcome

The `allow` object MAY contain:

| Field       | Type   | Required | Description                     |
|-------------|--------|----------|---------------------------------|
| `message`   | string | OPTIONAL | Human-readable allow reason     |
| `log_level` | string | OPTIONAL | Logging severity (`info`, `debug`) |

**Example**:
```yaml
decision:
  allow:
    message: "User authorized to access resource"
    log_level: info
```

### 6.3 Deny Outcome

The `deny` object MAY contain:

| Field       | Type   | Required | Description                     |
|-------------|--------|----------|---------------------------------|
| `message`   | string | OPTIONAL | Human-readable deny reason      |
| `log_level` | string | OPTIONAL | Logging severity (`warn`, `error`) |
| `code`      | string | OPTIONAL | Error code for client response  |

**Example**:
```yaml
decision:
  deny:
    message: "User not authorized to access this resource"
    log_level: warn
    code: FORBIDDEN
```

### 6.4 Constrain Outcome

The `constrain` object MAY contain:

| Field         | Type   | Required | Description                          |
|---------------|--------|----------|--------------------------------------|
| `constraints` | array  | REQUIRED | List of constraints to apply         |
| `message`     | string | OPTIONAL | Human-readable constraint reason     |

**Example**:
```yaml
decision:
  constrain:
    constraints:
      - "result.fields = ['id', 'name']"  # Limit fields in response
      - "result.limit = 100"               # Limit result set size
    message: "Access granted with field restrictions"
```

### 6.5 Warn Outcome

The `warn` object MAY contain:

| Field       | Type   | Required | Description                     |
|-------------|--------|----------|---------------------------------|
| `message`   | string | REQUIRED | Warning message                 |
| `log_level` | string | OPTIONAL | Logging severity (default: `warn`) |

**Example**:
```yaml
decision:
  warn:
    message: "User accessing resource outside normal hours"
    log_level: warn
```

---

## 7. Applicability Targets

### 7.1 Target Object

The `target` field MAY contain:

| Field        | Type   | Required | Description                              |
|--------------|--------|----------|------------------------------------------|
| `capability` | string | OPTIONAL | OCC capability ID this policy applies to |
| `function`   | string | OPTIONAL | OFC function ID this policy applies to   |
| `interaction`| string | OPTIONAL | OIC interaction ID this policy applies to|
| `event`      | string | OPTIONAL | OEC event ID this policy applies to      |
| `operations` | array  | OPTIONAL | List of operations (e.g., `read`, `write`)|

**Example**:
```yaml
target:
  capability: user-data-service
  operations: ["read", "update", "delete"]
```

### 7.2 Target Semantics

If `target` is **not specified**, the policy applies **globally** to all matching contexts.

If `target` is **specified**, the policy applies **only** to the specified capability, function, interaction, or event.

---

## 8. Policy Scope

### 8.1 Scope Field

The `scope` field MUST be one of:

| Value        | Meaning                                           |
|--------------|---------------------------------------------------|
| `global`     | Policy applies across entire system               |
| `service`    | Policy applies to a specific service              |
| `capability` | Policy applies to a specific capability (OCC)     |

**Example**:
```yaml
scope: global
```

### 8.2 Scope Hierarchy

Policies are evaluated in this order (most specific to least specific):
1. `capability` scope — Specific to one capability
2. `service` scope — Specific to one service
3. `global` scope — System-wide

**Note**: Conflict resolution is engine-specific and out of scope for OPC.

---

## 9. Compatibility Rules

### 9.1 Forward Compatibility

Policy engines MUST:
- Ignore unknown fields in received policies
- Not fail when encountering new decision outcomes
- Handle policies with higher MINOR versions gracefully

### 9.2 Backward Compatibility

Policy authors MUST NOT:
- Change condition semantics when incrementing MINOR or PATCH version
- Remove inputs when incrementing MINOR or PATCH version
- Change decision outcomes in a breaking way

### 9.3 Major Version Migration

When incrementing MAJOR version:
- Old and new policies MAY be enforced simultaneously (dual enforcement)
- Systems SHOULD support both versions during transition period
- A deprecation notice MUST be published for the old version

---

## 10. Relationship to Other Standards

### 10.1 Dependencies

**OPC depends on**:
- **OSC (Operational Semantic Context)** — For semantic context as condition inputs
- **OCD (Open Contract Document)** — For document structure
- **OCC (Open Capability Contract)** — For capability targeting

**OPC MAY reference**:
- **OIC (Open Intent Contract)** — For intent targeting
- **OEC (Open Event Contract)** — For event targeting
- **OFC (Open Function Contract)** — For function targeting

### 10.2 Distinctions

**OPC vs OHC (Open Hook Contract)**:
- **OHC** defines where code can be injected (extension points)
- **OPC** defines what is allowed (constraints)
- Hooks enable customization; policies enforce rules

**OPC vs OEC (Open Event Contract)**:
- **OEC** defines facts that happened (immutable, past tense)
- **OPC** defines what is allowed (constraints, present/future tense)
- Events are observations; policies are constraints

**OPC vs OSC (Operational Semantic Context)**:
- **OSC** describes what is known (context, metadata, semantics)
- **OPC** defines what is permitted (decisions based on context)
- Context is input; policy is the decision function

### 10.3 Integration

**Policy-to-Hook Integration**:
Policies MAY be evaluated at hook execution points, but policy contracts are independent of hook definitions.

**Policy-to-Event Integration**:
Events MAY trigger policy re-evaluation, but policies do not depend on event schemas.

---

## 11. Conformance

### 11.1 Conformant Policy

A policy is **OPC-conformant** if:
1. It contains all REQUIRED fields from Section 2.1
2. All fields conform to the data types specified
3. The `id` field follows the naming conventions in Section 3
4. The `version` field follows semantic versioning
5. The `condition.expression` is a valid declarative expression
6. The `decision` object contains at least one outcome

### 11.2 Validation

Conformance MAY be validated using:
- JSON Schema validation (see `schema/opc/0.1.0/schema.json`)
- Expression syntax validation (language-specific)
- Custom validation rules for domain-specific constraints

### 11.3 Non-Conformance Handling

Systems SHOULD:
- Reject non-conformant policies at registration
- Log validation errors with details
- Provide clear error messages indicating which constraints were violated

---

## Appendix A: Canonical Clarification

> **Hooks** define where extension is possible.
> **Events** define what happened.
> **Context** defines what is known.
> **Policies** define what is allowed.

OPC formalizes **what is allowed** as declarative, versioned constraints.

---

## Appendix B: Design Rationale

**Why declarative?**
Declarative policies separate "what to enforce" from "how to enforce it," enabling policy portability across engines.

**Why engine-independent?**
Policy contracts should not couple to OPA, Cedar, or any specific engine. This allows the same policy to be evaluated by different engines.

**Why versioned?**
Policy versioning enables auditability (what was enforced at time T?) and safe evolution (MAJOR/MINOR/PATCH semantics).

**Why OSC integration?**
Policies need contextual inputs (who, what, when, where). OSC provides the semantic layer for these inputs.

---

## Appendix C: Migration from Framework-Specific Policies

To migrate existing policies to OPC:

1. **Extract policy identity** — Define `id` based on existing policy name
2. **Add versioning** — Assign semantic version based on current enforcement rules
3. **Convert conditions** — Translate framework-specific syntax to CEL or supported language
4. **Map decision outcomes** — Ensure allow/deny semantics are preserved
5. **Validate against OPC** — Ensure conformance with OPC schema

**Example Migration (OPA Rego → OPC)**:

**Before (OPA Rego)**:
```rego
package authz

default allow = false

allow {
    input.subject.role == "admin"
}

allow {
    input.subject.id == input.resource.owner_id
}
```

**After (OPC)**:
```yaml
policy: 0.1.0
id: auth.access-control.resource-access
name: Resource Access Policy
version: 1.0.0
status: active
scope: global

condition:
  expression: "subject.role == 'admin' OR subject.id == resource.owner_id"
  inputs:
    - subject.role
    - subject.id
    - resource.owner_id

decision:
  allow:
    message: "Access granted"
  deny:
    message: "Access denied"
```

---

## Implementation Notes (Non-Normative)

This section provides guidance for implementing OPC-compliant policy systems. These notes are **non-normative** and do not impose additional requirements beyond the specification.

### Typical Pitfalls

**Pitfall 1: Mixing Policy and Implementation**

Embedding implementation details (database queries, API calls) in policies couples them to specific systems.

**Solution**: Keep policies declarative. Define conditions and effects only. Let the policy engine handle enforcement.

**Pitfall 2: Non-Deterministic Policies**

Policies that depend on time, random values, or external state are non-deterministic and hard to test.

**Solution**: Make policies pure functions of their inputs. Use explicit time parameters if time-based decisions are needed.

**Pitfall 3: Overlapping Policy Scopes**

Multiple policies with overlapping scopes can conflict, leading to unpredictable behavior.

**Solution**: Define clear scope boundaries. Implement conflict resolution rules (e.g., most specific wins, deny overrides allow).

**Pitfall 4: Missing Policy Scope**

Policies without explicit scope apply globally, which can cause unintended side effects.

**Solution**: Always define policy scope explicitly. Use narrow scopes (specific resources/capabilities) over broad scopes (global).

### Minimal Viable Implementation

A minimal OPC implementation requires:

1. **Policy Storage**: Store policies as JSON/YAML files or in a database
2. **Policy Evaluation Engine**: Implement condition evaluation (expression parser or rule engine)
3. **Scope Matching**: Match policies to resources/capabilities based on scope
4. **Effect Resolution**: Determine final effect when multiple policies apply
5. **Audit Logging**: Log all policy evaluations for compliance and debugging

### Example Policy Evaluation

**Scenario**: Evaluate "AdminOnly" policy for a user attempting to delete another user

**Steps**:
1. Load policy by ID: `com.example.AdminOnlyPolicy@1.0.0`
2. Check policy scope: Does it apply to "DeleteUser" capability? Yes.
3. Evaluate condition: `user.role != 'admin'`
4. Get user context: `user.role = 'user'`
5. Evaluate: `'user' != 'admin'` → `true`
6. Apply effect: `deny`
7. Return decision: `{ "effect": "deny", "message": "Only admins can perform this action" }`
8. Log evaluation for audit

### Policy Conflict Resolution

**Scenario**: Two policies apply to the same action

**Policy 1**: `effect: allow` (scope: all users)
**Policy 2**: `effect: deny` (scope: non-admins)

**Resolution Strategies**:

1. **Deny Overrides**: If any policy denies, final effect is deny (most secure)
2. **Allow Overrides**: If any policy allows, final effect is allow (most permissive)
3. **Most Specific Wins**: Policy with narrower scope wins
4. **Priority-Based**: Policies have explicit priority numbers

**Recommendation**: Use "Deny Overrides" for security-critical systems.

### Performance Considerations

**Policy Caching**: Cache compiled policies to avoid repeated parsing.

**Lazy Evaluation**: Only evaluate policies that match the current scope.

**Index by Scope**: Index policies by scope for fast lookup (e.g., by capability ID, resource type).

**Batch Evaluation**: When evaluating multiple actions, reuse policy context.

**Short-Circuit Evaluation**: Stop evaluating once a definitive effect is determined (e.g., explicit deny).

### Testing Policies

**Unit Tests**: Test individual policy conditions with known inputs.

**Integration Tests**: Test policy evaluation in context with real resources.

**Compliance Tests**: Verify policies enforce required compliance rules.

**Example Test**:
```javascript
describe('AdminOnlyPolicy', () => {
  it('should deny non-admin users', () => {
    const policy = loadPolicy('com.example.AdminOnlyPolicy@1.0.0');
    const context = { user: { role: 'user' } };
    const result = evaluatePolicy(policy, context);
    expect(result.effect).toBe('deny');
  });

  it('should allow admin users', () => {
    const policy = loadPolicy('com.example.AdminOnlyPolicy@1.0.0');
    const context = { user: { role: 'admin' } };
    const result = evaluatePolicy(policy, context);
    expect(result.effect).toBe('allow');
  });
});
```

---

**End of Specification**
