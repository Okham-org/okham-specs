you# ODT — Open Design Tokens Examples

**Version**: 0.1.0  
**Status**: DRAFT

---

## Example 1: Basic Token Bundle

```json
{
  "odt": "0.1.0",
  "id": "okham.core.brand",
  "name": "OKHAM Core Brand Tokens",
  "description": "Primary brand tokens for OKHAM",
  "tokens": [
    {
      "id": "color-primary",
      "type": "color",
      "value": "#0FA958",
      "description": "Primary brand green"
    },
    {
      "id": "spacing-md",
      "type": "spacing",
      "value": 16,
      "description": "Default spacing unit"
    }
  ]
}
```

---

## Example 2: Alias Tokens

```json
{
  "odt": "0.1.0",
  "id": "okham.core.aliases",
  "name": "OKHAM Alias Tokens",
  "description": "Aliases for shared values",
  "tokens": [
    {
      "id": "color-success",
      "type": "color",
      "value": "#0FA958"
    },
    {
      "id": "color-success-strong",
      "type": "color",
      "value": { "alias": "color-success" }
    }
  ]
}
```

---

## Example 3: Typography Token

```json
{
  "odt": "0.1.0",
  "id": "okham.core.type",
  "name": "OKHAM Typography Tokens",
  "description": "Typography tokens for headings",
  "tokens": [
    {
      "id": "type-heading-lg",
      "type": "typography",
      "value": "700 32px/40px 'Spectral'"
    }
  ]
}
```
