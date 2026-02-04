# Open Validation Report (OVR) Schema Reference

**Version**: 0.1.0  
**Status**: DRAFT  
**Last Updated**: 2026-01-17

---

## Canonical Schemas

- OVR report schema: https://okham.org/schemas/ovr-olr/0.1.0/report.schema.json
- OLR ruleset schema: https://okham.org/schemas/ovr-olr/0.1.0/ruleset.schema.json

## Field Overview (OVR Report)

- `ovr`: OVR version string (SemVer)
- `run`: Run metadata (runId, timestamp, tool, rulesets, target)
- `summary`: Issue counts (errors, warnings, infos)
- `issues`: Array of issue objects
- `metadata`: Optional additional metadata

## Non-Normative Excerpt

```json
{
  "ovr": "0.1.0",
  "run": {
    "runId": "run-123",
    "timestamp": "2026-01-17T10:30:00Z",
    "tool": "okham-lint",
    "rulesets": ["okham.core.mvp"],
    "target": "docs/"
  },
  "summary": {
    "errors": 1,
    "warnings": 2,
    "infos": 0
  },
  "issues": []
}
```

## Notes

- These schemas are DRAFT and may change before finalization.
- The OLR ruleset structure is not yet defined; the ruleset schema remains permissive.
