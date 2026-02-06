# OLR Examples

## Minimal ruleset

```yaml
rulesetId: okham.core.mvp
version: 0.1.0
description: MVP lint rules for OKHAM standards
rules:
  - ruleId: GLOBAL-001
    severity: error
    scope: global
    selector:
      globs: ["**/*.{yaml,yml,json}"]
    assert: required_field
    params:
      path: "/id"
    message: Document must declare id
```
