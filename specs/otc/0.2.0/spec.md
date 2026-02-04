# Open Type Contract (OTC) Specification

## Version 0.2.0
## Status: DRAFT
## Last Updated: 2026-01-15

---

## 1. Introduction

This document specifies the Open Type Contract (OTC) v0.2.0, a unified type system for the OKHAM ecosystem.

### 1.1 Purpose

OTC provides:
- Stable semantic meaning for data types
- Structural schema (JSON Schema-compatible)
- Interoperability between capabilities
- Foundation for automatic UI generation
- Hooks for internationalization (i18n) and accessibility (a11y)
- Validation beyond structural schemas
- Language bindings for Go and TypeScript

### 1.2 Single Source of Truth

**IMPORTANT**: The only authoritative source for type definitions is `/catalog/types/*.yaml`. This directory contains the canonical inventory. Documentation describes the standard but does not duplicate type definitions.

### 1.3 Scope

OTC covers:
- Canonical type definitions
- Structural schema (JSON Schema)
- Semantic meaning tags
- Field-level constraints
- Optional UI/i18n/a11y hints
- Inheritance (extends) and composition (mixins)
- Language implementations (Go, TypeScript)
- Versioning and compatibility rules

OTC does NOT cover:
- Full domain ontologies
- Storage models
- Business logic
- Transport protocols

---

## 2. Canonical Definition

### 2.1 What OTC Is

The Open Type Contract is a **semantic layer** on top of structural schemas. While JSON Schema defines *shape*, OTC defines *meaning*.

An OTC type declaration answers:
- What does this data represent?
- How should it be validated semantically?
- How can it be presented to users?
- What accessibility considerations apply?

### 2.2 What OTC Is Not

OTC is explicitly **not**:
- A replacement for JSON Schema (OTC builds on it)
- A programming language type system
- An ontology or knowledge graph
- A database schema language
- A business rule engine

---

## 3. OTC Core Structure

### 3.1 Type Definition

A Canonical Type MUST include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `otc` | string | Yes | OTC version (e.g., "0.2.0") |
| `id` | string | Yes | Unique namespaced identifier |
| `name` | string | Yes | Human-readable name |
| `description` | string | Yes | Purpose description |
| `fields` | array | Yes | Field definitions |

A Canonical Type MAY include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `version` | string | No | Type version (semver, defaults to "1.0.0") |
| `status` | string | No | Stability status (draft, stable, deprecated) |
| `schema` | object | No | JSON Schema representation |
| `extends` | string | No | Parent type ID for inheritance |
| `mixins` | array | No | Mixin type IDs for composition |
| `abstract` | boolean | No | If true, cannot be instantiated directly |
| `deprecated` | boolean | No | If true, type is deprecated |
| `deprecatedReason` | string | No | Reason for deprecation |
| `tags` | array | No | Classification tags |
| `metadata` | object | No | Additional metadata |
| `implementations` | array | No | Language-specific bindings |
| `examples` | array | No | Example values |

### 3.2 Type Identifier Format

Type identifiers MUST follow the pattern:

```
{namespace}.{domain}.{name}
```

Examples:
- `okham_core_user`
- `okham_core_address`
- `okham_geo_coordinate`
- `acme.crm.customer`

Reserved namespaces:
- `okham.core` — Core OKHAM types
- `okham.geo` — Geographic types
- `okham.time` — Temporal types
- `okham.money` — Financial types

---

## Closed-World Schema Note (v0.0.1)

The OTC JSON Schemas use `additionalProperties: false` at the root and in definitions. This closed-world assumption is intentional for v0.0.1. Future extensibility will require a minor or major version bump. This is a stability decision, not an oversight.
