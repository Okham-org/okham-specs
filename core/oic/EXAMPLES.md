# OIC Examples

**Version**: 0.1.0
**Status**: DRAFT

> **Disclaimer**: This document is non-normative and describes recommended practices for the OKHAM ecosystem.

---

## Intent Envelope Example

```yaml
oic: "0.1.0"
id: "intent-42"
intent: "decide"
purpose: "Choose whether to close the ticket"
constraints:
  policy: "helpdesk.close-permission"
expectations:
  outcome: "Decision cites criteria"
transitions:
  - "plan"
  - "execute"
```

---

## Intent Registry Block Example

```yaml
registry: "oic.intent.v0.1"
intents:
  - id: "analyze"
    description: "Understand, classify, diagnose; no proposals or execution"
  - id: "propose"
    description: "Generate options or alternatives; no execution"
  - id: "execute"
    description: "Produce effects; requires policy allowance and prior decide/plan"
```
