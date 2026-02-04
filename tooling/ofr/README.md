# OFR — Open Function Registry

**Version**: 0.1.0  
**Status**: DRAFT

## Overview

"OFR — Open Function Registry: Declarative specification of executable operations, including inputs, outputs, side effects, and intent compatibility, independent of language and runtime."

OFR provides a standardized way to describe executable operations (functions, methods, procedures) as semantic units that can be discovered, invoked, and composed across different systems and runtimes.

## Scope

OFR defines:

- **Operation Signatures**: Input and output types, parameters, and return values
- **Semantic Metadata**: Intent compatibility, side effects, and behavioral contracts
- **Discovery and Composition**: How operations can be cataloged and referenced
- **Runtime Independence**: Specifications that work across languages and execution environments

## Non-Goals

OFR does NOT:

- Define a runtime or execution environment
- Provide programming language abstractions
- Replace OSR (Open Snippet Registry) — OFR is for executable operations, OSR is for code fragments
- Specify implementation details or performance characteristics

## Relationship to Other Standards

### OIC — Open Intent Contract (Core)
OFR operations are annotated with intent compatibility metadata that aligns with OIC-defined intent semantics. OFR enables intent-preserving operation invocation and composition.

### OPC — Open Policy Contract (Core)
OFR operation metadata includes side effects and behavioral constraints that can be evaluated against OPC policies. This enables policy-aware operation selection and execution governance.

### OCC — Open Capability Contract (Tooling)
OCC packages executable capabilities that may expose OFR-defined operations. OFR provides the semantic interface layer that OCC uses for capability composition and interoperability.

### OSR — Open Snippet Registry (Tooling)
OSR catalogs reusable code fragments. OFR catalogs executable operations. They are complementary: OSR for code reuse, OFR for operation semantics and invocation.

## Maturity

OFR is **DRAFT** for v0.0.1. Breaking changes are allowed. Schemas may tighten further in minor or major releases.
