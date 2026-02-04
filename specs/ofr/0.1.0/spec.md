# OKHAM OFR — Open Function Registry
- Contract: OFR
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/ofr/0.1.0/schema.json

## Abstract

This specification defines the Open Function Registry (OFR) format for describing executable operations as semantic units independent of language and runtime.

## 1. Introduction

OFR enables systems to describe, discover, and invoke operations with well-defined semantics, inputs, outputs, and behavioral contracts.

## 2. Operation Definition

An OFR operation consists of:

- **Identifier**: Unique reference for the operation
- **Signature**: Input and output type specifications
- **Semantics**: Intent compatibility and side effect declarations
- **Metadata**: Versioning, authorship, and discovery information

## 3. Type System Integration

OFR operations reference OTC (Open Type Contract) types for input/output specifications, ensuring semantic consistency across systems.

## 4. Intent Compatibility

Operations declare compatibility with OIC (Open Intent Contract) intent types, enabling intent-preserving operation selection and composition.

## 5. Policy Integration

Operation metadata includes side effect declarations that can be evaluated against OPC (Open Policy Contract) policies.

## 6. Registry Format

OFR defines a registry format for cataloging operations, with search, filtering, and composition capabilities.

## 7. Examples

See [EXAMPLES.md](EXAMPLES.md) for detailed usage examples.

## 8. Schema

See [SCHEMA.md](SCHEMA.md) for the complete JSON Schema definition.

## 9. Implementation Notes

This is a draft specification. Implementation details will be refined based on ecosystem feedback and integration testing.</content>
<parameter name="filePath">docs/tooling/ofr/SPECIFICATION.md
