# OTC Implementations Guide

This guide covers how to use and generate language-specific implementations from OTC types.

---

## Overview

The `implementations` section in OTC types provides:

1. **Type-safe bindings** for consuming types in typed languages
2. **IDE support** for autocomplete and validation
3. **Code generation** from canonical type definitions
4. **Consistency** across language implementations

---

## Supported Languages

| Language | Status | Notes |
|----------|--------|-------|
| Go | Stable | Full struct support with json tags |
| TypeScript | Stable | Interface definitions |
| Java | Planned | Record classes |
| Python | Planned | TypedDict / dataclasses |

---

## Go Implementation

### Generated Code

```yaml
implementations:
  - language: "go"
    generated: true
    generation:
      source: "schema"
      tool: "okham-ctc-gen"
      version: "0.2.0"
    code: |
      package okhamcore

      import (
        "time"
      )

      type User struct {
        ID          string    `json:"id"`
        Email       string    `json:"email"`
        DisplayName string    `json:"displayName"`
        Status      string    `json:"status"`
        CreatedAt   time.Time `json:"createdAt"`
        UpdatedAt   time.Time `json:"updatedAt,omitempty"`
      }

      func (u User) IsActive() bool {
        return u.Status == "active"
      }
```

### Go Code Conventions

1. **Package**: Use `okham{domain}` (e.g., `okhamcore`, `okhamgeo`)
2. **JSON Tags**: Always include for serialization
3. **Omitempty**: Use for optional fields
4. **Time Handling**: Use `time.Time` for date-time fields
5. **Custom Types**: Generate enum types for constrained strings

### Custom Methods

Add custom methods to the generated struct for domain logic:

```go
func (u User) IsActive() bool {
  return u.Status == "active"
}

func (u User) CanAccess(permission string) bool {
  // Domain logic
}
```

---

## TypeScript Implementation

### Generated Code

```yaml
implementations:
  - language: "typescript"
    generated: true
    generation:
      source: "schema"
      tool: "okham-ctc-gen"
      version: "0.2.0"
    code: |
      export type UserStatus = 'active' | 'inactive' | 'pending' | 'suspended';

      export interface User {
        id: string;
        email: string;
        displayName: string;
        givenName?: string;
        familyName?: string;
        avatar?: string;
        status: UserStatus;
        locale?: string;
        timezone?: string;
        createdAt: string;
        updatedAt?: string;
      }

      export const DEFAULT_USER_STATUS: UserStatus = 'pending';
```

### TypeScript Conventions

1. **Type Aliases**: Use for enum values
2. **Optional Fields**: Use `?` for optional fields
3. **Import/Export**: Use ES modules
4. **Const Values**: Export default values for enums

---

## Code Generation

### Using okham-ctc-gen

```bash
# Generate Go code from type
okham-ctc-gen --lang go --input catalog/types/user.yaml --output pkg/types/user.go

# Generate TypeScript from type
okham-ctc-gen --lang typescript --input catalog/types/user.yaml --output src/types/user.ts

# Generate all implementations
okham-ctc-gen --all --input catalog/types/ --output generated/
```

### Generator Options

| Option | Description |
|--------|-------------|
| `--lang` | Target language (go, typescript) |
| `--input` | Input file or directory |
| `--output` | Output file or directory |
| `--package` | Go package name |
| `--namespace` | TypeScript namespace/module |
| `--strict` | Generate strict types (no `any`) |

---

## Manual Implementation

If code generation isn't available, implement manually:

### Go Example

```go
package main

import (
  "encoding/json"
  "time"
)

type UserStatus string

const (
  UserStatusActive    UserStatus = "active"
  UserStatusInactive  UserStatus = "inactive"
  UserStatusPending   UserStatus = "pending"
  UserStatusSuspended UserStatus = "suspended"
)

type User struct {
  ID          string    `json:"id"`
  Email       string    `json:"email"`
  DisplayName string    `json:"displayName"`
  Status      UserStatus `json:"status"`
  CreatedAt   time.Time `json:"createdAt"`
  UpdatedAt   *time.Time `json:"updatedAt,omitempty"`
}

func (u User) IsActive() bool {
  return u.Status == UserStatusActive
}

// UnmarshalJSON handles enum validation
func (u *User) UnmarshalJSON(data []byte) error {
  type Alias User
  aux := &struct {
    Status UserStatus `json:"status"`
    *Alias
  }{
    Alias: (*Alias)(u),
  }
  if err := json.Unmarshal(data, &aux); err != nil {
    return err
  }
  switch aux.Status {
  case UserStatusActive, UserStatusInactive, UserStatusPending, UserStatusSuspended:
    u.Status = aux.Status
  default:
    return &json.UnmarshalTypeError{
      Value: string(aux.Status),
      Type:  (*UserStatus)(nil),
    }
  }
  return nil
}
```

### TypeScript Example

```typescript
export type UserStatus = 'active' | 'inactive' | 'pending' | 'suspended';

export interface User {
  id: string;
  email: string;
  displayName: string;
  status: UserStatus;
  createdAt: string;
  updatedAt?: string;
}

export const DEFAULT_USER_STATUS: UserStatus = 'pending';

export function isActiveUser(user: User): boolean {
  return user.status === 'active';
}

export function validateUser(user: unknown): user is User {
  if (typeof user !== 'object' || user === null) {
    return false;
  }
  const u = user as Record<string, unknown>;
  return (
    typeof u['id'] === 'string' &&
    typeof u['email'] === 'string' &&
    typeof u['status'] === 'string' &&
    ['active', 'inactive', 'pending', 'suspended'].includes(u['status'] as string)
  );
}
```

---

## Validation

### Runtime Validation

Use generated types with validation libraries:

**Go**: `go-playground/validator`

```go
import "github.com/go-playground/validator/v10"

var validate = validator.New()

func ValidateUser(u *User) error {
  return validate.Struct(u)
}
```

**TypeScript**: `zod`

```typescript
import { z } from 'zod';

export const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  displayName: z.string().min(1).max(100),
  status: z.enum(['active', 'inactive', 'pending', 'suspended']),
  createdAt: z.string().datetime(),
});

export type User = z.infer<typeof UserSchema>;
```

---

## Best Practices

### 1. Don't Duplicate Logic

Keep validation in the canonical type, not in implementations.

### 2. Use Generated Code

Prefer code generation for consistency:

```bash
# In Makefile
generate-types:
  okham-ctc-gen --all --input catalog/types/ --output internal/types/
```

### 3. Version Your Types

Pin to specific type versions:

```yaml
mcp:
  tools:
    - id: get_user
      outputs:
        type: "$ref:okham_core_user#1.0.0"
```

### 4. Document Changes

When updating types, regenerate all implementations and update changelog.

---

## Troubleshooting

### Generated Code Doesn't Compile

1. Check for naming conflicts
2. Verify import paths
3. Ensure Go module is initialized

### TypeScript Type Errors

1. Check for `null` vs `undefined` handling
2. Verify date-time format strings
3. Check enum value consistency

---

## Related Documentation

- [CTC Specification](SPECIFICATION.md)
- [CTC Schema Reference](SCHEMA.md)
- [Migration Guide](MIGRATION.md)
