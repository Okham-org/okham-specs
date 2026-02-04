# OFR Examples

> **Status**: Draft
> **Version**: 0.1.0

## Example 1: Simple Calculator Operation

```json
{
  "ofr": {
    "version": "0.1.0",
    "operations": [
      {
        "id": "calc.add",
        "name": "Add Numbers",
        "description": "Adds two numbers together",
        "inputs": [
          {
            "name": "a",
            "type": "okham.number",
            "required": true
          },
          {
            "name": "b",
            "type": "okham.number",
            "required": true
          }
        ],
        "outputs": [
          {
            "name": "result",
            "type": "okham.number"
          }
        ],
        "intent": "computation.arithmetic",
        "sideEffects": [],
        "version": "1.0.0"
      }
    ]
  }
}
```

## Example 2: Data Processing Operation with Side Effects

```json
{
  "ofr": {
    "version": "0.1.0",
    "operations": [
      {
        "id": "data.process",
        "name": "Process User Data",
        "description": "Processes user data and stores results",
        "inputs": [
          {
            "name": "userData",
            "type": "okham.user.profile",
            "required": true
          }
        ],
        "outputs": [
          {
            "name": "processedData",
            "type": "okham.processed.result"
          }
        ],
        "intent": "data.processing.batch",
        "sideEffects": [
          "database.write",
          "audit.log"
        ],
        "version": "1.0.0"
      }
    ]
  }
}
```

## Usage Scenarios

### Operation Discovery
Systems can query OFR registries to find operations by intent compatibility, input/output types, or side effect profiles.

### Policy Evaluation
OPC policies can evaluate operation side effects before allowing execution.

### Capability Composition
OCC capabilities can reference OFR operations to declare their available functionality.

## Implementation Notes

These examples demonstrate the basic structure. Real implementations should integrate with OTC type definitions and OIC intent specifications for full semantic interoperability.</content>
<parameter name="filePath">docs/tooling/ofr/EXAMPLES.md