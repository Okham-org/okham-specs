# Open Validation Report (OVR) Examples

**Version**: 0.1.0  
**Status**: DRAFT

---

## Example 1: Single Issue Report

```json
{
  "ovr": "0.1.0",
  "run": {
    "runId": "run-2026-01-17-001",
    "timestamp": "2026-01-17T10:12:30Z",
    "tool": "okham-lint",
    "rulesets": [{"rulesetId":"okham.core.mvp","version":"0.1.0"}],
    "target": "./docs"
  },
  "summary": {
    "errors": 1,
    "warnings": 0,
    "infos": 0
  },
  "issues": [
    {
      "ruleId": "GLOBAL-001",
      "severity": "error",
      "message": "Document must declare id and version",
      "file": "docs/standards/otc/examples/bad-type.yaml",
      "jsonPointer": "/id",
      "line": 3,
      "column": 1,
      "evidence": null,
      "suggestedFix": "Add id and version fields"
    }
  ]
}
```

---

## Example 2: Multiple Issues Report

```json
{
  "ovr": "0.1.0",
  "run": {
    "runId": "run-2026-01-17-002",
    "timestamp": "2026-01-17T10:15:40Z",
    "tool": "okham-lint",
    "rulesets": [
      {"rulesetId":"okham.core.mvp","version":"0.1.0"},
      {"rulesetId":"okham.core.semantic","version":"0.1.0"}
    ],
    "target": "./docs",
    "baseline": "./baseline"
  },
  "summary": {
    "errors": 1,
    "warnings": 1,
    "infos": 0
  },
  "issues": [
    {
      "ruleId": "OEC-REF-001",
      "severity": "error",
      "message": "Event payload type does not exist in OTC registry",
      "file": "docs/standards/oec/examples/bad-event.yaml",
      "jsonPointer": "/payload/type",
      "evidence": "https://okham.org/types/missing/1.0.0"
    },
    {
      "ruleId": "OUCC-REF-001",
      "severity": "warn",
      "message": "Component declares neither props nor slots",
      "file": "docs/standards/oucc/examples/bad-component.yaml",
      "jsonPointer": "/props",
      "evidence": []
    }
  ]
}
```
