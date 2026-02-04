# Open Accessibility Contract (OAC) Specification

## Version 0.1.0
## Status: DRAFT
## Last Updated: 2026-01-17

---

## 1. Introduction

This document specifies the Open Accessibility Contract (OAC) v0.1.0, an optional extension standard for declarative accessibility obligations and evidence-based compliance verification.

### 1.1 Purpose

OAC provides:
- Declarative accessibility requirements independent of UI frameworks
- Evidence-based compliance verification model
- Semantic preservation of accessibility obligations across implementations
- Machine-readable accessibility contracts for automated testing
- Governance integration for accessibility policy enforcement

### 1.2 Scope

OAC covers:
- Accessibility obligation declarations
- Evidence model for compliance verification
- Semantic role preservation (ARIA, keyboard, screen reader)
- Relationship to accessibility standards (WCAG, ARIA, etc.)
- Integration with OKHAM Core (OUCC, OPC, OEC, OTC)

OAC does NOT cover:
- Specific UI framework implementations (implementation detail)
- Assistive technology implementations (implementation detail)
- Accessibility testing tools (tooling concern)
- Compliance certification processes (governance concern)
- HTML/CSS/ARIA rendering specifics (implementation detail)

---

## 2. Canonical Definition

### 2.1 What OAC Is

The Open Accessibility Contract is a **semantic layer** for accessibility requirements. It defines the **meaning** of accessibility obligations, not their implementation.

An OAC declaration answers:
- What accessibility requirements apply to this component or interaction?
- What evidence demonstrates compliance?
- What semantic roles and behaviors are expected?
- How are accessibility obligations verified?

### 2.2 What OAC Is Not

OAC is explicitly **not**:
- A UI framework or component library
- A replacement for WCAG or ARIA specifications
- An accessibility testing tool or framework
- A compliance certification authority
- A prescriptive implementation guide

---

## 3. Core Concepts

### 3.1 Accessibility Obligation

An **Accessibility Obligation** is a declarative requirement that specifies accessibility criteria for a component or interaction.

An Accessibility Obligation MUST include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | Yes | Unique obligation identifier |
| `target` | string | Yes | Component or interaction identifier |
| `standard` | string | Yes | Accessibility standard reference (e.g., "WCAG 2.1 AA") |
| `requirements` | array | Yes | Specific accessibility criteria |

An Accessibility Obligation MAY include:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `priority` | string | No | Severity level |
| `evidence` | array | No | Evidence requirements |
| `exemptions` | array | No | Documented exemptions |
| `metadata` | object | No | Additional metadata |

---

## Closed-World Schema Note (v0.0.1)

The OAC JSON Schema uses `additionalProperties: false` at the root and for core definitions. This closed-world assumption is intentional for v0.0.1. Future extensibility will require a minor or major version bump. This is a stability decision, not an oversight.
