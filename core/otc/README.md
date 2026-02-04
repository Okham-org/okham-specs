# Open Type Contract (OTC)

**Version**: 0.1.0
**Status**: DRAFT
**Scope**: Semantic Type Definitions for Interoperability
**Audience**: Capability Developers, UI Generators, i18n/a11y Systems, Validators

---

## Overview

The Open Type Contract (OTC) defines a minimal semantic contract for data types used across the OKHAM ecosystem. OTC provides stable semantic meaning for types, enabling interoperability, automatic UI generation, internationalization (i18n), accessibility (a11y), and validation.

OTC is **not** a programming language type system, an ontology, or a database schema. It is a **semantic contract** that assigns meaning to data structures, allowing different capabilities to share and understand data consistently.

---

## Problem This Contract Solves

Without OTC, systems face **semantic ambiguity** in data exchange:

- A `string` field named "email" could be a username, a contact address, or a display label
- Validators don't know which constraints apply (format, length, pattern)
- UI generators can't determine appropriate input types or autocomplete hints
- i18n systems can't map fields to localized labels
- Accessibility tools lack semantic context for screen readers

**OTC provides canonical type definitions with explicit semantic meaning**, enabling:
- Interoperability across capabilities without custom adapters
- Automatic UI generation from type definitions
- Consistent validation rules across systems
- Proper i18n and a11y support

---

## What Breaks Without It

Without OTC:

1. **Data Exchange Fails** — Systems interpret the same field differently, causing integration bugs
2. **UI Generation Breaks** — Auto-generated forms use wrong input types (text instead of email, number instead of currency)
3. **Validation Inconsistency** — Each system applies different constraints to the same logical type
4. **i18n/a11y Gaps** — No standard way to attach localization keys or accessibility metadata
5. **Semantic Drift** — Type meaning changes across capability boundaries, breaking contracts

---

## What This Contract Owns (Authority)

OTC is the **authoritative contract** for:

- Canonical type definitions
- Semantic meaning tags
- Field-level constraints
- UI/i18n/a11y hints

---

## Maturity

OTC is **DRAFT** for v0.0.1. Breaking changes are allowed. Schemas may tighten further in minor or major releases.
