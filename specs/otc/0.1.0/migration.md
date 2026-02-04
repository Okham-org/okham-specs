# OTC Migration Guide

## Version 0.1.0 to 0.2.0

This guide helps migrate types from OTC v0.1.0 to v0.2.0.

---

## What's New in v0.2.0

1. **Schema Section**: Explicit JSON Schema representation
2. **Mixins Support**: Composition through reusable field bundles
3. **Implementations**: Language-specific bindings (Go, TypeScript)
4. **Enhanced Metadata**: Version, status, tags at top level

---

## Migration Checklist

For each type in `/catalog/types/`:

- [ ] Add `version` field (defaults to "1.0.0" if omitted)
- [ ] Add `status` field (recommended values: "draft", "stable", "deprecated")
- [ ] Add `schema` section derived from fields
- [ ] Identify and add `mixins` where appropriate
- [ ] Add `implementations` stubs for Go + TypeScript
- [ ] Validate schema-fields consistency
- [ ] Run `okham lint catalog --strict`

---

## Step 1: Add Version and Status

```yaml
# Before (v0.1.0)
otc: "0.1.0"
id: "okham_core_user"
name: "User"
description: "A system user"
fields: [...]

# After (v0.2.0)
otc: "0.2.0"
id: "okham_core_user"
name: "User"
description: "A system user"
version: "1.0.0"
status: "stable"
fields: [...]
```

**Status Values**:
| Status | Meaning |
|--------|---------|
| `draft` | Under development, may change |
| `stable` | Production-ready, backward compatible changes only |
| `deprecated` | Scheduled for removal, use alternatives |

---

## Step 2: Generate Schema Section

The `schema` section is derived from `fields`. Here's the mapping:

```yaml
fields:
  - name: "id"
    type: "string"
    meaning: "id.uuid"
    constraints:
      required: true
      format: "uuid"

# Generates schema property:
schema:
  properties:
    id:
      type: string
      format: uuid
  required: [id]
```

**Schema Generation Rules**:
1. Copy `type` to schema property `type`
2. Copy `constraints` validation keywords (minLength, maxLength, etc.)
3. Copy `constraints.format` to schema `format`
4. Copy `constraints.enum` to schema `enum`
5. Add field name to `required` array if `constraints.required: true`

---

## Step 3: Identify Mixins

Look for common field patterns across types:

| Pattern | Mixin to Add |
|---------|--------------|
| `createdAt` + `updatedAt` | `okham.mixin.timestamp` |
| `id` + `version` | `okham.mixin.identity` |
| `createdAt` + `updatedAt` + `createdBy` + `updatedBy` | `okham.mixin.audit` |

```yaml
# Before
fields:
  - name: "createdAt"
    type: "string"
    meaning: "time.created"
  - name: "updatedAt"
    type: "string"
    meaning: "time.updated"

# After
mixins:
  - "okham.mixin.timestamp"

fields:
  # Timestamp fields can be removed as they're in the mixin
  # OR kept for additional constraints/hints
  - name: "createdAt"
    type: "string"
    meaning: "time.created"
    hints:
      ui:
        hidden: true
```

---

## Step 4: Add Implementations

Add language bindings for Go and TypeScript:

```yaml
implementations:
  - language: "go"
    generated: true
    generation:
      source: "schema"
      tool: "okham-ctc-gen"
    code: |
      type User struct {
        ID          string    `json:"id"`
        Email       string    `json:"email"`
        DisplayName string    `json:"displayName"`
        Status      string    `json:"status"`
        CreatedAt   time.Time `json:"createdAt"`
      }

  - language: "typescript"
    generated: true
    generation:
      source: "schema"
      tool: "okham-ctc-gen"
    code: |
      export interface User {
        id: string;
        email: string;
        displayName: string;
        status: 'active' | 'inactive' | 'pending' | 'suspended';
        createdAt: string;
      }
```

---

## Step 5: Validate Migration

Run the following commands to validate your migration:

```bash
# Check catalog structure
okham lint catalog

# Validate schema-fields consistency
okham validate ctc --schema-strict

# Check implementations
okham validate implementations

# Full strict validation
okham lint --strict
```

---

## Common Migration Issues

### Issue: Schema doesn't match fields

**Problem**: Schema properties don't match field definitions.

**Solution**: Regenerate schema from fields or manually align them.

```bash
# Auto-generate schema from fields
okham generate schema --input user.yaml --output user.yaml
```

### Issue: Missing required field in schema

**Problem**: Field has `constraints.required: true` but not in schema.required.

**Solution**: Add field to schema.required array.

### Issue: Invalid type reference

**Problem**: Field type is not a primitive or valid `$ref`.

**Solution**: Use `$ref:okham_core_typename` format for type references.

---

## Rollback

If migration causes issues, rollback by:

1. Revert changes to type file
2. Run `okham lint catalog` to verify
3. Document the issue for the team

---

## Related Documentation

- [CTC Specification](SPECIFICATION.md)
- [CTC Schema Reference](SCHEMA.md)
- [Implementations Guide](IMPLEMENTATIONS.md)
