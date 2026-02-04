# OSR — Open Snippet Registry Examples

**Version**: 0.1.0  
**Status**: DRAFT

---

## Example 1: Minimal Registry

```json
{
  "osr": "0.1.0",
  "id": "okham.core.snippets",
  "name": "OKHAM Core Snippets",
  "description": "Reusable OKHAM snippets",
  "snippets": [
    {
      "id": "capability-readme",
      "name": "Capability README",
      "version": "1.0.0",
      "language": "markdown",
      "content": "# Capability\n\nDescribe the capability here."
    }
  ]
}
```

---

## Example 2: Structured Content

```json
{
  "osr": "0.1.0",
  "id": "okham.core.templates",
  "name": "Template Registry",
  "description": "Template snippets",
  "snippets": [
    {
      "id": "policy-template",
      "name": "Policy Template",
      "version": "0.1.0",
      "language": "yaml",
      "content": {
        "format": "yaml",
        "body": "policy: allow\nrule: example"
      },
      "tags": ["policy", "template"]
    }
  ]
}
```

---

## Example 3: Dependencies and Compatibility

```json
{
  "osr": "0.1.0",
  "id": "okham.core.oucc",
  "name": "OUCC Snippets",
  "description": "Reusable OUCC fragments",
  "snippets": [
    {
      "id": "button-primary",
      "name": "Primary Button",
      "version": "0.2.0",
      "language": "json",
      "content": "{\"component\":\"okham.ui.button\"}",
      "dependencies": ["okham.ui.core"],
      "compatibility": ["oucc:1.x"]
    }
  ]
}
```
