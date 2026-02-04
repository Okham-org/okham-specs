# OKCC Examples

Real-world capability examples demonstrating the OKHAM Capability Contract.

---

## Table of Contents

1. [Minimal Capability](#1-minimal-capability)
2. [User Management Capability](#2-user-management-capability)
3. [File Manager Capability](#3-file-manager-capability)
4. [API-Only Capability](#4-api-only-capability)
5. [UI-Only Capability](#5-ui-only-capability)
6. [MCP-Only Capability](#6-mcp-only-capability)
7. [JSON-RPC API Capability](#7-json-rpc-api-capability)

---

## 1. Minimal Capability

The smallest valid capability contract:

```yaml
contractVersion: capability.contract.v1

id: hello-world
name: Hello World
version: 1.0.0
description: >
  A simple capability that demonstrates the minimal required fields.
  This capability publishes greeting events.

subjects:
  publishes:
    - "env.*.t.*.s.*.hello-world.evt.greeting.>"
  examples:
    - "env.dev.t.default.s.jaca.hello-world.evt.greeting.created"
```

**Key Points:**
- Only 5 required fields
- No HTTP, UI, or MCP declarations
- Minimal subject declaration

---

## 2. User Management Capability

A full-featured user management capability with HTTP, MCP, and UI:

```yaml
contractVersion: capability.contract.v1

id: user-mgmt
name: User Management
version: 1.2.0
description: >
  Manages user accounts, authentication, and permissions.
  Provides user CRUD operations, role management, and session handling.
  Used by the admin panel and other capabilities.

tags:
  - users
  - authentication
  - management
  - core

visibility: public
icon: users

domainsOwned:
  - user-management

crossDomainPublishes:
  - domain: analytics
    reason: "Publishes user activity metrics for analytics"
  - domain: audit
    reason: "Publishes audit events for compliance"

streams:
  reads:
    - USER_EVENTS_STREAM
  writes:
    - AUDIT_LOG_STREAM
    - SESSION_EVENTS_STREAM

kv:
  reads:
    - GLOBAL_CONFIG_KV
    - USER_PREFERENCES_KV
  writes:
    - USER_DATA_KV
    - SESSION_KV

interestGroups:
  - name: user-services
    members:
      - user-mgmt
      - authentication
      - session-manager
      - permissions

subjects:
  publishes:
    - "env.*.t.*.s.*.user-mgmt.evt.user.>"       # User events
    - "env.*.t.*.s.*.user-mgmt.evt.session.>"    # Session events
    - "env.*.t.*.s.*.user-mgmt.logs.>"           # Logs
    - "env.*.t.*.s.*.user-mgmt.err.>"            # Errors
    - "env.*.t.*.s.*.user-mgmt.audit.>"          # Audit events
  examples:
    - "env.dev.t.default.s.jaca.user-mgmt.evt.user.created"
    - "env.dev.t.default.s.jaca.user-mgmt.evt.user.updated"
    - "env.dev.t.default.s.jaca.user-mgmt.evt.user.deleted"
    - "env.dev.t.default.s.jaca.user-mgmt.evt.session.created"
    - "env.dev.t.default.s.jaca.user-mgmt.audit.user.login"

subscriptions:
  nats:
    - pattern: "env.*.t.*.s.*.user-mgmt.cmd.user.create"
      description: "Create user command handler"
    - pattern: "env.*.t.*.s.*.user-mgmt.cmd.user.update"
      description: "Update user command handler"
    - pattern: "env.*.t.*.s.*.user-mgmt.cmd.user.delete"
      description: "Delete user command handler"
    - pattern: "env.*.t.*.s.*.user-mgmt.cmd.user.get"
      description: "Get user command handler"
    - pattern: "env.*.t.*.s.*.user-mgmt.cmd.session.validate"
      description: "Validate session command handler"
  jetstream:
    - subject: "env.*.t.*.s.*.auth.evt.session.>"
      stream: SESSION_EVENTS_STREAM
      durable: user-mgmt-session-consumer
      description: "Consume session events for user tracking"
    - subject: "env.*.t.*.s.*.permissions.evt.>"
      stream: PERMISSIONS_STREAM
      durable: user-mgmt-permissions-consumer
      description: "Consume permission changes"

http:
  basePath: /api/user-mgmt
  targetBase: http://user-mgmt:8080
  endpoints:
    - method: GET
      path: /users
      purpose: List all users with pagination and filtering
      response: application/json
    - method: POST
      path: /users
      purpose: Create a new user
      response: application/json
    - method: GET
      path: /users/{id}
      purpose: Get user by ID
      response: application/json
    - method: PUT
      path: /users/{id}
      purpose: Update user
      response: application/json
    - method: PATCH
      path: /users/{id}
      purpose: Partial update user
      response: application/json
    - method: DELETE
      path: /users/{id}
      purpose: Delete user (soft delete)
      response: application/json
    - method: POST
      path: /users/{id}/roles
      purpose: Assign roles to user
      response: application/json
    - method: DELETE
      path: /users/{id}/roles/{roleId}
      purpose: Remove role from user
      response: application/json
    - method: GET
      path: /users/{id}/sessions
      purpose: Get user sessions
      response: application/json
    - method: DELETE
      path: /users/{id}/sessions
      purpose: Invalidate all user sessions
      response: application/json
    - method: GET
      path: /roles
      purpose: List all roles
      response: application/json
    - method: POST
      path: /roles
      purpose: Create new role
      response: application/json
    - method: GET
      path: /roles/{id}
      purpose: Get role by ID
      response: application/json
    - method: PUT
      path: /roles/{id}
      purpose: Update role
      response: application/json
    - method: DELETE
      path: /roles/{id}
      purpose: Delete role
      response: application/json

ui:
  nav:
    group: Administration
    order: 10
    icon: users
  pages:
    - id: user-list
      path: /users
      title: User Management
      layout: core.page@1
      blocks:
        - component: core.header@1
          props:
            title: User Management
            subtitle: Manage users, roles, and permissions
        - component: user-mgmt.user-table@1
          props:
            showFilters: true
            showSearch: true
            pageSize: 25
            actions:
              - view
              - edit
              - delete
    - id: user-detail
      path: /users/{id}
      title: User Details
      layout: core.page@1
      blocks:
        - component: core.header@1
          props:
            title: "User: {{user.displayName}}"
        - component: user-mgmt.user-detail@1
          props:
            showActivity: true
            showRoles: true
            showSessions: true
    - id: role-list
      path: /roles
      title: Role Management
      layout: core.page@1
      blocks:
        - component: core.header@1
          props:
            title: Role Management
        - component: user-mgmt.role-table@1
          props:
            showPermissions: true

mcp:
  tools:
    - id: user@create
      description: Create a new user with the provided information
      method: POST
      path: /api/user-mgmt/users
      params:
        - name: email
          type: string
          required: true
          description: User email address (unique)
        - name: displayName
          type: string
          required: true
          description: Display name for the user
        - name: firstName
          type: string
          required: false
          description: User first name
        - name: lastName
          type: string
          required: false
          description: User last name
        - name: roles
          type: array
          required: false
          description: Roles to assign to user
        - name: metadata
          type: object
          required: false
          description: Additional metadata
    - id: user@get
      description: Get user by ID
      method: GET
      path: /api/user-mgmt/users/{id}
      params:
        - name: id
          type: string
          required: true
          description: User ID (UUID)
    - id: user@list
      description: List users with pagination and filtering
      method: GET
      path: /api/user-mgmt/users
      params:
        - name: page
          type: number
          required: false
          description: Page number (1-indexed, default: 1)
        - name: limit
          type: number
          required: false
          description: Items per page (default: 20, max: 100)
        - name: search
          type: string
          required: false
          description: Search query (matches email, displayName)
        - name: status
          type: string
          required: false
          description: Filter by status (active, inactive, suspended)
        - name: role
          type: string
          required: false
          description: Filter by role
    - id: user@update
      description: Update user information
      method: PUT
      path: /api/user-mgmt/users/{id}
      params:
        - name: id
          type: string
          required: true
          description: User ID
        - name: displayName
          type: string
          required: false
          description: New display name
        - name: firstName
          type: string
          required: false
          description: New first name
        - name: lastName
          type: string
          required: false
          description: New last name
        - name: metadata
          type: object
          required: false
          description: Updated metadata
    - id: user@delete
      description: Delete user (soft delete)
      method: DELETE
      path: /api/user-mgmt/users/{id}
      params:
        - name: id
          type: string
          required: true
          description: User ID to delete
    - id: user@validate-email
      description: Check if email is available
      method: GET
      path: /api/user-mgmt/users/validate-email
      params:
        - name: email
          type: string
          required: true
          description: Email to validate
    - id: role@create
      description: Create a new role
      method: POST
      path: /api/user-mgmt/roles
      params:
        - name: name
          type: string
          required: true
          description: Role name
        - name: description
          type: string
          required: false
          description: Role description
        - name: permissions
          type: array
          required: true
          description: List of permission strings
    - id: role@list
      description: List all roles
      method: GET
      path: /api/user-mgmt/roles

imports:
  - type: User@1.0.0
  - type: Address@1.0.0
  - snippet: user@validate@1.0.0
  - snippet: user@transform-from-external@1.0.0

limits:
  timeout: 30000
  rateLimit: "100/minute"

auth:
  required: false
```

---

## 3. File Manager Capability

Capability for file operations with streams and events:

```yaml
contractVersion: capability.contract.v1

id: file-manager
name: File Manager
version: 1.0.0
description: >
  Handles file uploads, downloads, and management.
  Provides file storage, metadata tracking, and access control.

visibility: public

domainsOwned:
  - file-storage

streams:
  writes:
    - FILE_EVENTS_STREAM

kv:
  reads:
    - FILE_METADATA_KV
    - FILE_PERMISSIONS_KV
  writes:
    - FILE_METADATA_KV

subjects:
  publishes:
    - "env.*.t.*.s.*.file-manager.evt.file.>"
    - "env.*.t.*.s.*.file-manager.err.>"
  examples:
    - "env.dev.t.default.s.jaca.file-manager.evt.file.uploaded"
    - "env.dev.t.default.s.jaca.file-manager.evt.file.deleted"

http:
  basePath: /api/file-manager
  targetBase: http://file-manager:8080
  endpoints:
    - method: POST
      path: /files
      purpose: Upload a file
      response: application/json
    - method: GET
      path: /files
      purpose: List files with filtering
      response: application/json
    - method: GET
      path: /files/{id}
      purpose: Get file metadata
      response: application/json
    - method: GET
      path: /files/{id}/download
      purpose: Download file content
      response: application/octet-stream
    - method: DELETE
      path: /files/{id}
      purpose: Delete file
      response: application/json

mcp:
  tools:
    - id: file@upload
      description: Upload a file
      method: POST
      path: /api/file-manager/files
    - id: file@list
      description: List files
      method: GET
      path: /api/file-manager/files
    - id: file@download
      description: Download a file
      method: GET
      path: /api/file-manager/files/{id}/download
    - id: file@delete
      description: Delete a file
      method: DELETE
      path: /api/file-manager/files/{id}
```

---

## 4. API-Only Capability

A capability that only exposes HTTP API (no UI, no MCP):

```yaml
contractVersion: capability.contract.v1

id: analytics-api
name: Analytics API
version: 1.0.0
description: >
  Provides analytics data and metrics through HTTP API.
  Used by other capabilities for reporting.

visibility: public

domainsOwned:
  - analytics

subjects:
  publishes:
    - "env.*.t.*.s.*.analytics.evt.report.>"

http:
  basePath: /api/analytics
  targetBase: http://analytics-api:8080
  endpoints:
    - method: GET
      path: /metrics
      purpose: Get system metrics
      response: application/json
    - method: GET
      path: /reports
      purpose: List available reports
      response: application/json
    - method: GET
      path: /reports/{id}
      purpose: Get report data
      response: application/json
```

---

## 5. UI-Only Capability

A capability that only provides UI (admin panel extension):

```yaml
contractVersion: capability.contract.v1

id: system-settings
name: System Settings
version: 1.0.0
description: >
  Admin panel for system configuration.
  No backend logic, purely UI component.

visibility: public

http: {}

ui:
  nav:
    group: Administration
    order: 100
    icon: settings
  pages:
    - id: settings
      path: /settings
      title: System Settings
      layout: core.page@1
      blocks:
        - component: core.header@1
          props:
            title: System Settings
        - component: system-settings.settings-form@1
          props:
            sections:
              - General
              - Security
              - Integrations
```

---

## 6. MCP-Only Capability

A capability that only exposes MCP tools (for AI agents):

```yaml
contractVersion: capability.contract.v1

id: ai-helper
name: AI Helper
version: 1.0.0
description: >
  Provides AI-related tools for other capabilities.
  Exposes functionality through MCP for AI agent consumption.

visibility: public

mcp:
  tools:
    - id: ai@generate-text
      description: Generate text using configured AI model
      method: POST
      path: /api/ai/generate
      params:
        - name: prompt
          type: string
          required: true
          description: The prompt for text generation
        - name: maxTokens
          type: number
          required: false
          description: Maximum tokens to generate
        - name: temperature
          type: number
          required: false
          description: Generation temperature (0-1)
    - id: ai@embed-text
      description: Generate text embeddings
      method: POST
      path: /api/ai/embed
      params:
        - name: text
          type: string
          required: true
          description: Text to embed
```

---

## 7. JSON-RPC API Capability

A capability that exposes JSON-RPC 2.0 methods for external API access, complementing MCP tools for AI agents.

```yaml
contractVersion: capability.contract.v1

id: payment-processor
name: Payment Processor
version: 1.0.0
description: >
  Processes payments and manages transactions with JSON-RPC API access.

subjects:
  publishes:
    - "env.*.t.*.s.*.payment-processor.evt.payment.>"
  examples:
    - "env.dev.t.default.s.jaca.payment-processor.evt.payment.completed"

# JSON-RPC API for external clients
jsonrpc:
  enabled: true
  basePath: "/api/rpc"
  methods:
    - name: "processPayment"
      description: "Process a payment transaction"
      params:
        type: object
        properties:
          amount:
            type: number
            description: "Payment amount in cents"
          currency:
            type: string
            enum: ["USD", "EUR", "GBP"]
            description: "Payment currency"
          cardToken:
            type: string
            description: "Tokenized card information"
          description:
            type: string
            description: "Payment description"
        required: ["amount", "currency", "cardToken"]
      result:
        type: object
        properties:
          transactionId:
            type: string
            description: "Unique transaction identifier"
          status:
            type: string
            enum: ["pending", "completed", "failed"]
            description: "Transaction status"
          processedAt:
            type: string
            format: date-time
            description: "Processing timestamp"
      errors:
        - code: -32001
          message: "Invalid payment amount"
        - code: -32002
          message: "Card declined"
        - code: -32003
          message: "Insufficient funds"

    - name: "getTransaction"
      description: "Retrieve transaction details"
      params:
        type: object
        properties:
          transactionId:
            type: string
            description: "Transaction identifier"
        required: ["transactionId"]
      result:
        type: object
        properties:
          id:
            type: string
          amount:
            type: number
          currency:
            type: string
          status:
            type: string
          createdAt:
            type: string
            format: date-time

# MCP tools for AI agent integration
mcp:
  tools:
    - id: payment@process
      description: Process a payment through the payment processor
      inputSchema:
        type: object
        properties:
          amount:
            type: number
            description: Payment amount
          currency:
            type: string
            description: Currency code
          description:
            type: string
            description: Payment description

# UI for admin management
ui:
  nav:
    group: "Financial"
    order: 20
    icon: "credit-card"
  pages:
    - id: transactions
      title: "Payment Transactions"
      path: "/payments/transactions"
      layout: "table-view"
```

**JSON-RPC Usage:**

```bash
# Process payment
curl -X POST http://localhost:8080/api/rpc \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "capabilities.payment-processor.processPayment",
    "params": {
      "amount": 1000,
      "currency": "USD",
      "cardToken": "tok_123456",
      "description": "Invoice payment"
    },
    "id": 1
  }'

# Response
{
  "jsonrpc": "2.0",
  "result": {
    "transactionId": "txn_789",
    "status": "completed",
    "processedAt": "2024-01-15T10:30:00Z"
  },
  "id": 1
}
```

**Key Points:**
- JSON-RPC methods provide structured API access for external clients
- MCP tools enable AI agent integration with natural language
- Both can coexist, serving different integration patterns
- JSON-RPC supports batch requests and detailed error codes

---

## Key Patterns

### Pattern 1: Full-Stack Capability

```yaml
# Includes all features
- HTTP API
- MCP tools
- UI pages
- NATS subjects
- KV buckets
```

### Pattern 2: API Service

```yaml
# Focus on HTTP API
- HTTP endpoints
- NATS subjects
- KV buckets
```

### Pattern 3: Admin UI

```yaml
# Focus on UI
- UI pages
- HTTP (for form submissions)
```

### Pattern 4: AI Tools

```yaml
# Focus on MCP tools
- MCP declarations
- HTTP backing
```

---

## Anti-Patterns to Avoid

### Anti-Pattern 1: Missing Subjects
```yaml
# DON'T: No subject declarations for a capability that should publish
subjects: {}
```

### Anti-Pattern 2: Overly Broad Wildcards
```yaml
# DON'T: Wildcards that are too broad
publishes:
  - "env.*.t.*.s.*.#.>"  # Too broad!
```

### Anti-Pattern 3: Missing Description
```yaml
# DON'T: Empty or minimal description
description: "A capability"
```

### Anti-Pattern 4: Wrong Contract Version
```yaml
# DON'T: Using wrong version
contractVersion: v1  # Wrong!
```
