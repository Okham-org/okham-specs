# OKHAM OBC — Open Business Contract
- Contract: OBC
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/obc/0.1.0/schema.json
- Last Updated: 2026-01-17

---

## 1. Introduction

This document specifies the Open Business Contract (OBC) v0.1.0, an optional extension standard for declarative business flow composition and orchestration with semantic meaning preservation.

### 1.1 Purpose

OBC provides:
- Declarative business process definitions independent of execution engines
- Semantic preservation of business intent across system boundaries
- Composability of business flows from reusable components
- Observability of business process execution through events
- Governance integration for business rules and policies

### 1.2 Scope

OBC covers:
- Business flow structure (steps, transitions, conditions)
- Semantic intent preservation across implementations
- State semantics and transitions
- Composition rules (nesting, referencing, parameterization)
- Integration with OKHAM Core (OTC, OEC, OPC)

OBC does NOT cover:
- Workflow engine implementations (implementation detail)
- Specific execution models or runtimes (implementation detail)
- UI for designing business processes (tooling concern)
- Monitoring or analytics platforms (tooling concern)
- Runtime execution semantics (implementation detail)

---

## 2. Canonical Definition

### 2.1 What OBC Is

The Open Business Contract is a **semantic layer** for business process composition. It defines the **meaning** of business flows, not their execution.

An OBC declaration answers:
- What business process is being defined?
- What steps comprise the process?
- How do steps transition based on conditions?
- What state is tracked throughout execution?
- How does this process compose with other processes?

### 2.2 What OBC Is Not

OBC is explicitly **not**:
- A workflow engine or orchestration platform
- A specific execution model (sequential, parallel, event-driven)
- A business process modeling tool (BPMN, etc.)
- A runtime or execution environment
- A monitoring or analytics system

---

## 3. Core Concepts

### 3.1 Business Flow

A **Business Flow** is a declarative definition of a business process.

A Business Flow MUST include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | Unique flow identifier |
| `name` | string | Yes | Human-readable flow name |
| `description` | string | Yes | Purpose description |
| `steps` | array | Yes | Flow steps |

A Business Flow MAY include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `version` | string | No | Flow version (semver) |
| `inputs` | array | No | Input parameters (OTC references) |
| `outputs` | array | No | Output parameters (OTC references) |
| `state` | object | No | State definition |
| `policies` | array | No | Policy references (OPC) |
| `metadata` | object | No | Additional metadata |

---

## Closed-World Schema Note (v0.0.1)

The OBC JSON Schema uses `additionalProperties: false` at the root and for core definitions. This closed-world assumption is intentional for v0.0.1. Future extensibility will require a minor or major version bump. This is a stability decision, not an oversight.
