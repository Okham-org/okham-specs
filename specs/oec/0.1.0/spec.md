# Open Event Contract (OEC) — Specification

**Version**: 0.1.0
**Status**: DRAFT
**Last Updated**: 2026-01-16

---

## Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

---

## Table of Contents

1. [Introduction](#1-introduction)
2. [Event Structure](#2-event-structure)
3. [Event Identity](#3-event-identity)
4. [Event Versioning](#4-event-versioning)
5. [Payload Definition](#5-payload-definition)
6. [Metadata Requirements](#6-metadata-requirements)
7. [Event Classification](#7-event-classification)
8. [Compatibility Rules](#8-compatibility-rules)
9. [Relationship to Other Standards](#9-relationship-to-other-standards)
10. [Conformance](#10-conformance)

---

## 1. Introduction

### 1.1 Purpose

The Open Event Contract (OEC) defines a **normative contract for events as observable, versioned, public facts** that can be published and consumed across systems.

### 1.2 Scope

OEC specifies:

- Event structure and required fields
- Event identity and naming conventions
- Event versioning and compatibility rules
- Payload definition via OTC integration
- Metadata requirements (timestamp, source, causality)
- Event classification taxonomy

### 1.3 Non-Goals

OEC explicitly **does NOT define**:

- Event handlers, consumers, or subscribers (application logic)
- Transport mechanisms (NATS, Kafka, HTTP, etc.)
- Delivery guarantees (at-least-once, exactly-once)
- Event execution or lifecycle hooks (see OHC)
- Event storage or persistence mechanisms

### 1.4 Event Semantics

An **event** is:
- A **fact** that occurred in the past (immutable)
- Expressed in **past tense** (e.g., "UserCreated", not "CreateUser")
- **Self-contained** with all necessary context
- **Observable** by external systems
- **Versioned** for evolution

**Events are NOT**:
- Commands (requests to do something)
- Queries (requests for information)
- Responses (replies to interactions)
- Hooks (extension points)

---

## 2. Event Structure

### 2.1 Root Object

Every OEC event MUST be a structured document containing:

| Field            | Type    | Required | Description                          |
|------------------|---------|----------|--------------------------------------|
| `event`          | string  | REQUIRED | OEC version (semantic version)       |
| `id`             | string  | REQUIRED | Event identifier (unique, namespaced)|
| `name`           | string  | REQUIRED | Human-readable event name            |
| `version`        | string  | REQUIRED | Event schema version                 |
| `status`         | string  | REQUIRED | Lifecycle status                     |
| `source`         | object  | REQUIRED | Event source information             |
| `timestamp`      | string  | REQUIRED | ISO 8601 timestamp (UTC)             |
| `causality`      | object  | OPTIONAL | Causation and correlation tracking   |
| `classification` | object  | OPTIONAL | Event classification metadata        |
| `payload`        | object  | REQUIRED | Event data (OTC-typed)               |
| `metadata`       | object  | OPTIONAL | Additional custom metadata           |

### 2.2 Event Field

The `event` field MUST:
- Contain the OEC version as a semantic version string
- Match the pattern: `^[0-9]+\.[0-9]+\.[0-9]+$`
- Indicate which version of OEC the event conforms to

**Example**:
```yaml
event: 0.1.0
```

---

## 3. Event Identity

### 3.1 Event ID

The `id` field MUST:
- Uniquely identify the event schema across the system
- Use reverse-domain notation or hierarchical namespacing
- Be lowercase with dot separators
- Match the pattern: `^[a-z][a-z0-9._-]*[a-z0-9]$`
- Be stable across versions (event ID does not change when version changes)

**Recommended Structure**:
```
<domain>.<subdomain>.<lifecycle|action>
```

**Examples**:
- `user.lifecycle.created`
- `order.payment.processed`
- `inventory.stock.depleted`

### 3.2 Event Name

The `name` field MUST:
- Provide a human-readable description of the event
- Use Title Case
- Express the event in **past tense**
- Be concise (recommended: 2-5 words)

**Examples**:
- `User Created Event`
- `Order Payment Processed`
- `Stock Depleted Alert`

---

## 4. Event Versioning

### 4.1 Version Field

The `version` field MUST:
- Follow semantic versioning (MAJOR.MINOR.PATCH)
- Increment MAJOR for breaking changes
- Increment MINOR for backward-compatible additions
- Increment PATCH for clarifications or non-functional changes

### 4.2 Breaking Changes

A change is **breaking** if it:
- Removes a required field
- Changes the type of an existing field
- Changes the semantics of an existing field
- Renames a field
- Changes validation rules in a way that rejects previously valid events

### 4.3 Non-Breaking Changes

A change is **non-breaking** if it:
- Adds a new optional field
- Adds new enum values
- Relaxes validation rules
- Adds clarifying documentation

### 4.4 Status Field

The `status` field MUST be one of:
- `draft` — Under development, not stable
- `stable` — Production-ready, backward compatibility guaranteed
- `deprecated` — Superseded, use discouraged
- `removed` — No longer supported

---

## 5. Payload Definition

### 5.1 Payload Object

The `payload` field MUST contain:

| Field  | Type   | Required | Description                    |
|--------|--------|----------|--------------------------------|
| `type` | string | REQUIRED | OTC type URL or reference      |
| `data` | object | REQUIRED | Actual event data (type-conformant) |

### 5.2 Type Reference

The `type` field MUST:
- Reference an OTC type definition
- Use a fully-qualified URL (HTTPS) or OTC identifier
- Include the type version

Event payloads SHOULD reference **fact payload types**, not request DTOs. Payload types SHOULD NOT end with `Request`, `-request`, or `_request`.

**Fact payload naming guidance**:
- Use past-tense event IDs with present-tense payload types (e.g., `ticket.created` event uses `ticket` payload)
- Favor domain nouns for payload types (e.g., `ticket`, `comment`, `status-change`)
- Reserve `*Request` types for OIC interactions, not OEC events

**Example**:
```yaml
payload:
  type: "https://okham.org/types/user/1.0.0"
  data:
    user_id: usr_abc123
    email: user@example.com
```

### 5.3 Data Validation

The `data` object MUST conform to the schema defined by the `type` reference.

---

## 6. Metadata Requirements

### 6.1 Source Object

The `source` field MUST contain:

| Field        | Type   | Required | Description                  |
|--------------|--------|----------|------------------------------|
| `capability` | string | REQUIRED | OCC capability ID that emitted the event |
| `instance`   | string | OPTIONAL | Instance ID of the emitting capability |
| `version`    | string | OPTIONAL | Version of the emitting capability |

**Example**:
```yaml
source:
  capability: user-management-service
  instance: 7f8a9b3c
  version: 2.1.0
```

### 6.2 Timestamp

The `timestamp` field MUST:
- Be an ISO 8601 datetime string
- Include timezone (preferably UTC)
- Represent when the event occurred (not when it was published)

**Format**: `YYYY-MM-DDTHH:MM:SS.sssZ`

**Example**:
```yaml
timestamp: 2026-01-16T10:30:00.123Z
```

### 6.3 Causality Object

The `causality` field MAY contain:

| Field            | Type   | Required | Description                        |
|------------------|--------|----------|------------------------------------|
| `correlation_id` | string | OPTIONAL | Trace ID for related events        |
| `causation_id`   | string | OPTIONAL | ID of the event/command that caused this event |
| `trace_id`       | string | OPTIONAL | Distributed tracing identifier     |
| `span_id`        | string | OPTIONAL | Tracing span identifier            |

**Purpose**: Enables event causality tracking across distributed systems.

**Example**:
```yaml
causality:
  correlation_id: req-uuid-12345
  causation_id: cmd-create-user-67890
  trace_id: trace-abc-def-123
```

---

## 7. Event Classification

### 7.1 Classification Object

The `classification` field MAY contain:

| Field      | Type   | Required | Description                      |
|------------|--------|----------|----------------------------------|
| `domain`   | string | OPTIONAL | Event domain (dot-separated)     |
| `lifecycle`| string | OPTIONAL | Lifecycle phase                  |
| `severity` | string | OPTIONAL | Event severity level             |
| `scope`    | string | OPTIONAL | Event visibility scope           |
| `category` | string | OPTIONAL | OSC semantic category            |

### 7.2 Domain Classification

The `domain` field SHOULD:
- Match the first part of the event `id`
- Use hierarchical dot notation
- Represent functional or business domain

**Examples**: `user.lifecycle`, `order.payment`, `inventory.stock`

### 7.3 Severity Levels

The `severity` field MAY be one of:
- `debug` — Development/debugging information
- `info` — Informational state changes
- `warn` — Warning or degraded state
- `error` — Error or failure condition
- `critical` — Critical system failure

### 7.4 Scope

The `scope` field MAY be one of:
- `public` — Event visible to external systems
- `internal` — Event for internal system use only
- `private` — Event restricted to originating service

---

## 8. Compatibility Rules

### 8.0 Policy Denial Events

Events whose `id` ends with `.policy.denied` MUST use the canonical policy decision payload type: `okham.policy.decision@1.0.0`.

### 8.1 Forward Compatibility

Consumers MUST:
- Ignore unknown fields in received events
- Not fail when encountering new optional fields
- Handle events with higher MINOR versions gracefully

### 8.2 Backward Compatibility

Producers MUST NOT:
- Remove required fields when incrementing MINOR or PATCH version
- Change field types when incrementing MINOR or PATCH version
- Change field semantics when incrementing MINOR or PATCH version

### 8.3 Major Version Migration

When incrementing MAJOR version:
- Producers MAY publish both old and new versions simultaneously (dual-write)
- Consumers SHOULD support both versions during transition period
- A deprecation notice MUST be published for the old version

---

## 9. Relationship to Other Standards

### 9.1 Dependencies

**OEC depends on**:
- **OTC (Open Type Contract)** — For payload type definitions
- **OCD (Open Contract Document)** — For document structure
- **OSC (Operational Semantic Context)** — For semantic classification

### 9.2 Distinctions

**OEC vs OHC (Open Hook Contract)**:
- **OHC** defines extension points where code can be injected
- **OEC** defines facts that occurred (past tense, immutable)
- Hooks are proactive; events are reactive

**OEC vs OIC (Open Intent Contract)**:
- **OIC** defines intent declarations (purpose, constraints, transitions)
- **OEC** defines observable facts that occurred
- Intent declares what is sought; events report what happened

**OEC vs OCC (Open Capability Contract)**:
- **OCC** defines capabilities that expose functions
- **OEC** defines events that capabilities emit
- Capabilities are invoked; events are observed

### 9.3 Integration

**Event-to-Hook Mapping**:
Events MAY trigger hooks, but the event contract is independent of hook execution.

**Event-to-Function Mapping**:
OFC functions MAY emit events, but event schemas are independent of function signatures.

---

## 10. Conformance

### 10.1 Conformant Event

An event is **OEC-conformant** if:
1. It contains all REQUIRED fields from Section 2.1
2. All fields conform to the data types specified
3. The `id` field follows the naming conventions in Section 3
4. The `version` field follows semantic versioning
5. The `payload.type` references a valid OTC type
6. The `timestamp` is a valid ISO 8601 datetime

### 10.2 Validation

Conformance MAY be validated using:
- JSON Schema validation (see `schema/oec/0.1.0/schema.json`)
- OTC type validation for payload
- Custom validation rules for domain-specific constraints

### 10.3 Non-Conformance Handling

Systems SHOULD:
- Reject non-conformant events at ingress boundaries
- Log validation errors with details
- Provide clear error messages indicating which constraints were violated

---

## Appendix A: Canonical Clarification

> **Hooks** define where extension is possible.
> **Events** define what happened.
> **Context** defines what is known.
> **Policies** define what is allowed.

OEC formalizes **what happened** as immutable, versioned facts.

---

## Appendix B: Design Rationale

**Why past tense?**
Events represent facts that already occurred. Past tense naming enforces this semantic distinction from commands (future intent).

**Why immutable?**
Event immutability enables event sourcing, audit logs, and time-travel debugging. Once emitted, events cannot be changed.

**Why transport-independent?**
Event contracts should not couple to Kafka topics, NATS subjects, or HTTP endpoints. This allows the same event schema to be used across multiple transport mechanisms.

**Why causality tracking?**
Distributed tracing and root cause analysis require event causality chains. Correlation IDs enable this without transport coupling.

---

## Appendix C: Migration from Proprietary Events

To migrate existing events to OEC:

1. **Extract event identity** — Define `id` based on existing event name/topic
2. **Add versioning** — Assign semantic version based on current schema
3. **Wrap payload** — Move existing event data into `payload.data`
4. **Add metadata** — Include `source`, `timestamp`, `causality`
5. **Validate against OTC** — Ensure payload conforms to OTC type

---

## Implementation Notes (Non-Normative)

This section provides guidance for implementing OEC-compliant event systems. These notes are **non-normative** and do not impose additional requirements beyond the specification.

### Typical Pitfalls

**Pitfall 1: Reusing Event IDs**

Event IDs must be globally unique. Reusing IDs breaks causality tracking and event deduplication.

**Solution**: Use UUIDs, ULIDs, or a distributed ID generator. Never reuse event IDs, even after deletion.

**Pitfall 2: Mutable Events**

Modifying events after emission breaks the immutability guarantee and corrupts causality chains.

**Solution**: Treat events as write-once. If correction is needed, emit a new compensating event with causation pointing to the original.

**Pitfall 3: Missing Causation Chains**

Omitting causation breaks distributed tracing and makes root cause analysis impossible.

**Solution**: Always populate the `causation` array with parent event IDs. Use empty array `[]` only for root events.

**Pitfall 4: Generic Event Types**

Event types like "DataChanged" or "Update" provide no semantic value.

**Solution**: Use domain-specific event types with full namespace qualification (e.g., `com.example.UserEmailChanged@1.0.0`).

### Minimal Viable Implementation

A minimal OEC implementation requires:

1. **Event Storage**: Store events in an append-only log (event store, database, message queue)
2. **ID Generation**: Use a UUID/ULID generator for unique event IDs
3. **Timestamp Generation**: Use UTC timestamps in ISO 8601 format
4. **Causation Tracking**: Maintain parent event IDs in context
5. **Schema Validation**: Validate event structure against OEC schema

### Example Event Emission

**Scenario**: Emit a "UserCreated" event after creating a user

**Steps**:
1. Generate unique event ID: `evt_01HQZX3Y4Z5A6B7C8D9E0F1G2H`
2. Get current UTC timestamp: `2024-01-16T10:30:00Z`
3. Get causation from context (e.g., from triggering HTTP request event)
4. Get correlation ID from context (e.g., from request tracing)
5. Construct event with type, source, data, causation, correlation
6. Validate event against OEC schema
7. Emit event to event store/stream
8. Return event ID for downstream causation

### Causality Chain Example

**Scenario**: User registration flow

```
1. HTTPRequestReceived (root event, causation=[])
   └─> 2. UserCreated (causation=[evt_1])
       ├─> 3. EmailSent (causation=[evt_2])
       └─> 4. ProfileInitialized (causation=[evt_2])
```

**Implementation**:
- Event 1: `causation: []` (root)
- Event 2: `causation: ["evt_1"]` (caused by HTTP request)
- Event 3: `causation: ["evt_2"]` (caused by user creation)
- Event 4: `causation: ["evt_2"]` (caused by user creation)

### Performance Considerations

**Batch Emission**: Emit multiple events in a single transaction when possible.

**Async Processing**: Process events asynchronously to avoid blocking the main flow.

**Event Replay**: Design consumers to be idempotent (handle duplicate events gracefully).

**Partitioning**: Partition event streams by aggregate ID or correlation ID for parallel processing.

**Retention**: Define retention policies based on compliance and debugging needs (e.g., 90 days for operational events, indefinite for audit events).

---

**End of Specification**
