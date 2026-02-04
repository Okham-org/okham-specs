# OFR Schema

> **Status**: DRAFT
> **Version**: 0.1.0

## Canonical Schema

Canonical schema: https://okham.org/schemas/ofr/0.1.0/function.schema.json

## Field Overview

- `ofr.version`: OFR specification version
- `ofr.operations`: Array of operation definitions
- `operation.id`: Unique operation identifier
- `operation.intent`: OIC intent compatibility token
- `operation.sideEffects`: Declared side effects

## Non-Normative Excerpt

```json
{
  "ofr": {
    "version": "0.1.0",
    "operations": [
      {
        "id": "calc.add",
        "name": "Add Numbers",
        "intent": "computation.arithmetic"
      }
    ]
  }
}
```

## Notes

- This schema is DRAFT and may change before finalization.
- Type references should align with OTC definitions.
- Intent field should reference OIC intent types.
- Side effects should be evaluable against OPC policies.
