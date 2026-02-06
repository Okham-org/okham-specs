# OAS Examples

## okham.core.required_field@0.1.0

```yaml
oas: 0.1.0
assertionId: okham.core.required_field
version: 0.1.0
description: >
  Fails if the JSON Pointer `params.path` does not exist in the document.

olr:
  assert: required_field

paramsSchema:
  type: object
  additionalProperties: false
  required: [path]
  properties:
    path:
      type: string
      description: JSON Pointer to required location.
    alternatePath:
      type: string

examples:
  - title: Require id
    rule:
      ruleId: GLOBAL-001
      severity: error
      scope: global
      selector: { globs: ["**/*.{yaml,yml,json}"] }
      assert: required_field
      params: { path: "/id" }
      message: Document must declare id
```
