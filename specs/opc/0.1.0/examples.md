# Open Policy Contract (OPC) — Examples

**Version**: 0.1.0

> **Disclaimer**: This document is non-normative and describes recommended practices for the OKHAM ecosystem.

This document provides real-world examples of OPC-conformant policies.

---

## Table of Contents

1. [Authorization Policy](#1-authorization-policy)
2. [Rate Limiting Policy](#2-rate-limiting-policy)
3. [Data Governance Policy](#3-data-governance-policy)
4. [Time-Based Access Policy](#4-time-based-access-policy)
5. [Constrained Access Policy](#5-constrained-access-policy)
6. [Compliance Policy with Warnings](#6-compliance-policy-with-warnings)

---

## 1. Authorization Policy

**Scenario**: Users can only access their own data or admins can access all data.

```yaml
policy: 0.1.0
id: auth.access-control.user-data-access
name: User Data Access Policy
version: 1.0.0
status: active
scope: global

target:
  capability: user-data-service
  operations: ["read", "update", "delete"]

condition:
  expression: "subject.id == resource.owner_id OR subject.role == 'admin'"
  inputs:
    - subject.id
    - subject.role
    - resource.owner_id
  language: cel

decision:
  allow:
    message: "User authorized to access resource"
    log_level: info
  deny:
    message: "User not authorized to access this resource"
    log_level: warn
    code: FORBIDDEN

metadata:
  owner: security-team
  category: authorization
```

**Key Points**:
- Simple allow/deny logic
- Uses OSC context (`subject`, `resource`)
- Targets specific capability and operations
- Clear error messaging

---

## 2. Rate Limiting Policy

**Scenario**: API requests are limited to 100 per minute per user.

```yaml
policy: 0.1.0
id: rate-limit.api.requests-per-minute
name: API Rate Limit Policy
version: 1.0.0
status: active
scope: global

target:
  capability: api-gateway
  operations: ["*"]

condition:
  expression: "environment.requests_last_minute < 100"
  inputs:
    - environment.requests_last_minute
  language: cel

decision:
  allow:
    message: "Request within rate limit"
    log_level: debug
  deny:
    message: "Rate limit exceeded: 100 requests per minute"
    log_level: warn
    code: TOO_MANY_REQUESTS

metadata:
  owner: platform-team
  category: rate-limiting
  enforcement: hard-limit
```

**Key Points**:
- Uses environment context for rate tracking
- Global scope (applies to all users)
- Standard HTTP error code (`TOO_MANY_REQUESTS`)

---

## 3. Data Governance Policy

**Scenario**: PII data can only be accessed by users with `data-steward` role.

```yaml
policy: 0.1.0
id: compliance.data-governance.pii-access
name: PII Access Compliance Policy
version: 1.0.0
status: active
scope: global

target:
  capability: data-warehouse
  operations: ["read", "query"]

condition:
  expression: |
    resource.classification == 'PII' AND
    subject.role IN ['data-steward', 'compliance-officer']
  inputs:
    - resource.classification
    - subject.role
  language: cel

decision:
  allow:
    message: "User authorized to access PII data"
    log_level: info
  deny:
    message: "PII data access requires data-steward or compliance-officer role"
    log_level: error
    code: FORBIDDEN

metadata:
  owner: compliance-team
  category: data-governance
  regulation: GDPR
  audit_required: true
```

**Key Points**:
- Data classification-based policy
- Multi-role authorization (`IN` operator)
- Compliance metadata for auditing

---

## 4. Time-Based Access Policy

**Scenario**: Administrative functions can only be executed during business hours (9 AM - 5 PM UTC).

```yaml
policy: 0.1.0
id: auth.temporal.business-hours-only
name: Business Hours Access Policy
version: 1.0.0
status: active
scope: service

target:
  capability: admin-console
  operations: ["delete", "modify_config"]

condition:
  expression: |
    environment.time.hour >= 9 AND
    environment.time.hour < 17 AND
    environment.time.day_of_week IN [1, 2, 3, 4, 5]
  inputs:
    - environment.time.hour
    - environment.time.day_of_week
  language: cel

decision:
  allow:
    message: "Administrative action allowed during business hours"
    log_level: info
  deny:
    message: "Administrative actions restricted to business hours (9 AM - 5 PM UTC, Mon-Fri)"
    log_level: warn
    code: FORBIDDEN

metadata:
  owner: security-team
  category: temporal-access-control
```

**Key Points**:
- Time-based restrictions
- Uses environment context for temporal data
- Service-level scope

---

## 5. Constrained Access Policy

**Scenario**: Users can read all user data but only see limited fields unless they are admins.

```yaml
policy: 0.1.0
id: auth.access-control.field-level-restriction
name: Field-Level Access Restriction
version: 1.0.0
status: active
scope: global

target:
  capability: user-data-service
  operations: ["read", "list"]

condition:
  expression: "subject.role != 'admin'"
  inputs:
    - subject.role
  language: cel

decision:
  allow:
    message: "Full access granted"
    log_level: info
  constrain:
    constraints:
      - "result.fields = ['id', 'name', 'email']"  # Limit visible fields
      - "result.exclude_sensitive = true"
    message: "Access granted with field restrictions (non-admin user)"

metadata:
  owner: security-team
  category: field-level-security
```

**Key Points**:
- Uses `constrain` outcome (not just allow/deny)
- Limits response fields based on role
- Allows access but with restrictions

---

## 6. Compliance Policy with Warnings

**Scenario**: Warn when users access data outside their assigned region (but don't block).

```yaml
policy: 0.1.0
id: compliance.data-residency.cross-region-warning
name: Cross-Region Data Access Warning
version: 1.0.0
status: active
scope: global

target:
  capability: data-service
  operations: ["read", "write"]

condition:
  expression: "subject.region != resource.region"
  inputs:
    - subject.region
    - resource.region
  language: cel

decision:
  allow:
    message: "Same-region data access"
    log_level: debug
  warn:
    message: "User accessing data from different region (potential data residency issue)"
    log_level: warn

metadata:
  owner: compliance-team
  category: data-residency
  regulation: data-sovereignty
  alert_on_trigger: true
```

**Key Points**:
- Uses `warn` outcome (non-blocking)
- Logs but does not deny access
- Useful for compliance monitoring without disrupting operations

---

## 7. Multi-Condition Policy

**Scenario**: Complex policy with multiple conditions (IP allowlist + role check + time restriction).

```yaml
policy: 0.1.0
id: auth.access-control.multi-factor-policy
name: Multi-Factor Access Policy
version: 1.0.0
status: active
scope: capability

target:
  capability: financial-reporting-service
  operations: ["read", "export"]

condition:
  expression: |
    (subject.role == 'finance-manager' OR subject.role == 'cfo') AND
    environment.ip IN ['10.0.0.0/8', '192.168.1.0/24'] AND
    environment.time.hour >= 8 AND environment.time.hour < 18
  inputs:
    - subject.role
    - environment.ip
    - environment.time.hour
  language: cel

decision:
  allow:
    message: "User meets all access requirements (role, IP, time)"
    log_level: info
  deny:
    message: "Access denied: Must be finance-manager/cfo, from corporate IP, during work hours"
    log_level: error
    code: FORBIDDEN

metadata:
  owner: finance-security-team
  category: multi-factor-policy
  requirements:
    - role-based
    - ip-allowlist
    - time-restricted
```

**Key Points**:
- Combines multiple conditions (role AND IP AND time)
- Capability-level scope
- Clear failure reason in deny message

---

## 8. Policy Versioning Example

**Scenario**: A policy evolves from v1.0.0 to v2.0.0 (breaking change).

### Version 1.0.0 (Old - Only Admins)

```yaml
policy: 0.1.0
id: auth.access-control.sensitive-data
name: Sensitive Data Access Policy
version: 1.0.0
status: deprecated
scope: global

target:
  capability: sensitive-data-service

condition:
  expression: "subject.role == 'admin'"
  inputs:
    - subject.role

decision:
  allow:
    message: "Admin access granted"
  deny:
    message: "Only admins can access sensitive data"
    code: FORBIDDEN
```

### Version 2.0.0 (New - Admins OR Data Stewards)

```yaml
policy: 0.1.0
id: auth.access-control.sensitive-data
name: Sensitive Data Access Policy
version: 2.0.0
status: active
scope: global

target:
  capability: sensitive-data-service

condition:
  expression: "subject.role IN ['admin', 'data-steward']"  # BREAKING: Expanded roles
  inputs:
    - subject.role

decision:
  allow:
    message: "Authorized user (admin or data-steward)"
  deny:
    message: "Access requires admin or data-steward role"
    code: FORBIDDEN

metadata:
  changelog: "v2.0.0: Added data-steward role (breaking change)"
```

**Key Points**:
- `version` incremented to 2.0.0 (MAJOR change)
- Old version marked `deprecated`
- Breaking change: Expanded allowed roles
- Metadata includes changelog

---

## Anti-Patterns (What NOT to Do)

### ❌ Imperative Logic in Expression

```yaml
condition:
  expression: "if (subject.role == 'admin') { return true; } else { return false; }"
  # WRONG: This is imperative code, not a declarative expression
```

### ❌ Business Logic in Policy

```yaml
decision:
  allow:
    execute_hook: send_welcome_email  # WRONG: Policies don't execute business logic
```

### ❌ Coupling to Transport

```yaml
condition:
  expression: "request.http_method == 'POST'"
  # WRONG: Should not reference transport-specific details
```

### ❌ Side Effects in Condition

```yaml
condition:
  expression: "log_access(subject.id) AND subject.role == 'admin'"
  # WRONG: Expressions should be pure (no side effects)
```

---

**End of Examples**
