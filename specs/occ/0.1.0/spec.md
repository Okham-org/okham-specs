# OCC — Open Capability Contract

## Version 0.1.0
## Status: DRAFT

Open Capability Contract (OCC) defines the declarative YAML format for defining
capabilities in the OKHAM ecosystem. This specification was previously referred
to as "OKHAM Capability Contract".

---

## Table of Contents

1. [Overview](#1-overview)
2. [Conventions](#2-conventions)
3. [Schema Overview](#3-schema-overview)
4. [Contract Version](#4-contract-version)
5. [Capability Identification](#5-capability-identification)
6. [Visibility](#6-visibility)
7. [Domains](#7-domains)
8. [Resources](#8-resources)
9. [Subjects](#9-subjects)
10. [Subscriptions](#10-subscriptions)
11. [HTTP Configuration](#11-http-configuration)
12. [UI Declaration](#12-ui-declaration)
  - [12.3 Internationalization](#123-internationalization-i18n)
13. [MCP Tools](#13-mcp-tools)
15. [JSON-RPC Methods](#15-json-rpc-methods)
16. [LLM Context](#16-llm-context)
17. [Catalog Imports](#17-catalog-imports)
18. [Limits and Auth](#18-limits-and-auth)
19. [Governance](#19-governance)

---

## 1. Overview

Open Capability Contract (OCC) is a declarative specification that describes what a capability exposes to the OKHAM ecosystem. It defines:

- **Identity**: Name, version, and description
- **Resources**: NATS streams, KV buckets, and subjects
- **Interfaces**: HTTP endpoints and MCP tools
- **UI**: Admin pages and navigation
- **Behavior**: Limits, authentication, and lifecycle

OKCC files are written in YAML and typically named `capability.yaml`.

---

## 2. Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [BCP 14](https://datatracker.ietf.org/doc/html/rfc2119).

---

## 3. Schema Overview

```yaml
# REQUIRED: Contract version
contractVersion: capability.contract.v1

# REQUIRED: Capability identification
id: capability-name           # REQUIRED, kebab-case
name: Human Name              # REQUIRED
version: 1.0.0                # REQUIRED, semver
description: >                 # REQUIRED, multiline
  Description for LLM context

# OPTIONAL: Visibility
visibility: public | private   # Default: private

# OPTIONAL: Domain ownership
domainsOwned:
  - domain-name

# OPTIONAL: Cross-domain permissions
crossDomainPublishes:
  - domain: domain-name
    reason: Explanation

# OPTIONAL: Streams
streams:
  reads: [STREAM_NAME]
  writes: [STREAM_NAME]

# OPTIONAL: KV buckets
kv:
  reads: [KV_NAME]
  writes: [KV_NAME]

# OPTIONAL: Interest groups for resource sharing
# Capabilities in the same group share access to streams, KV buckets, and subjects
# Use interestGroups when multiple capabilities need to collaborate on shared state
interestGroups:
  - name: group-name
    members: [capability-id]
    sharedResources:
      - type: kv
        name: KV_BUCKET_NAME
        purpose: "Shared state for component registry"
      - type: stream
        name: STREAM_NAME
        purpose: "Shared event stream"

# OPTIONAL: KV-first pattern
# RECOMMENDED: Use KV for shared state instead of request/reply when:
# - Multiple capabilities need to access the same data
# - Data is read more often than written
# - Subscribers need to react to changes
# - Latency tolerance allows for eventual consistency
#
# Example KV configuration for shared component registry:
kv:
  reads: [COMPONENT_REGISTRY_KV]
  writes: [COMPONENT_REGISTRY_KV]

interestGroups:
  - name: ui-components
    members: [component-mgmt, html-ui-renderer, ui-shell]
    sharedResources:
      - type: kv
        name: COMPONENT_REGISTRY_KV
        purpose: "Shared component registry for UI rendering"

# REQUIRED: Subject declarations
subjects:
  publishes:
    - "env.*.t.*.s.*.domain.type.service.detail"
  examples:
    - "env.dev.t.default.s.jaca.domain.evt.service.entity.created"

# OPTIONAL: Subscriptions
subscriptions:
  nats:
    - pattern: "env.*.t.*.s.*.domain.cmd.service.method"
      description: Handler description
  jetstream:
    - subject: "env.*.t.*.s.*.domain.evt.>"
      stream: STREAM_NAME
      durable: consumer-name
      description: Description

# OPTIONAL: HTTP configuration
http:
  basePath: /api/capability-id
  targetBase: http://capability:8080
  endpoints:
    - method: GET|POST|PUT|DELETE
      path: /endpoint-path
      purpose: Description
      response: application/json

# OPTIONAL: UI declaration
ui:
  nav:
    group: Group Name
    order: 10
    icon: icon-name
  pages:
    - id: page-id
      path: /page-path
      title: Page Title
      layout: core.page@1
      blocks:
        - component: component-id@version
          props: {}

# OPTIONAL: MCP tools
mcp:
  tools:
    - id: tool-name
      description: Tool description
      inputSchema: {}

# OPTIONAL: LLM context for AI agents
llm:
  description: >                   # Enhanced description for LLM context
  rules:                            # Usage rules and constraints
    - when: condition
      then: action
      note: explanation
  skills:                           # Higher-level capabilities
    - name: skill-name
      description: Skill description
      tools: [tool-refs]
  commands:                         # Composite operations
    - name: command-name
      description: Command description
      steps: []
      example: {}
  knowledge:                        # Domain knowledge for LLM
    - domain: domain-name
      content: |

# OPTIONAL: Catalog imports
imports:
  - type: TypeName@version
  - snippet: snippetName@version

# OPTIONAL: LLM context for AI agent guidance
```

---

## 4. Contract Version

### 4.1 Required Version String

All OCC declarations MUST include the following `contractVersion` at the root level:

```yaml
contractVersion: capability.contract.v1
```

This value is required for version compatibility with tooling and validators.

---

## 5. Capability Identification

OCC capability IDs MUST:

- Use kebab-case (`lowercase-words-with-hyphens`)
- Be unique across the registry
- Be stable across versions

---

## 6. Visibility

Visibility determines how a capability is listed in registries.

Valid values:

- `public`
- `private`

If omitted, the default is `private`.

---

## 7. Domains

OCC defines domain ownership to ensure semantic clarity across capabilities.

### 7.1 `domainsOwned`

```yaml
domainsOwned:
  - domain1
  - domain2
```

### 7.2 `crossDomainPublishes`

```yaml
crossDomainPublishes:
  - domain: analytics
    reason: "Publishes metrics"
```

Each entry MUST include:

- `domain` (required)
- `reason` (required)

---

## 8. Resources

### 8.1 Streams

```yaml
streams:
  reads: [STREAM1, STREAM2]
  writes: [STREAM3]
```

### 8.2 KV Buckets

```yaml
kv:
  reads: [KV1, KV2]
  writes: [KV3]
```

### 8.3 Interest Groups

```yaml
interestGroups:
  - name: user-services
    members:
      - user-mgmt
      - authentication
```

---

## 9. Subjects

```yaml
subjects:
  publishes:
    - "env.*.t.*.s.*.domain.evt.service.>"
  examples:
    - "env.dev.t.default.s.jaca.domain.evt.service.entity.created"
```

---

## 10. Subscriptions

```yaml
subscriptions:
  nats:
    - pattern: "env.*.t.*.s.*.domain.cmd.service.method"
      description: Handler description
  jetstream:
    - subject: "env.*.t.*.s.*.domain.evt.>"
      stream: STREAM_NAME
      durable: consumer-name
      description: Description
```

---

## 11. HTTP Configuration

```yaml
http:
  basePath: /api/capability-id
  targetBase: http://capability:8080
  endpoints:
    - method: GET|POST|PUT|DELETE
      path: /endpoint-path
      purpose: Description
      response: application/json
```

---

## 12. UI Declaration

```yaml
ui:
  nav:
    group: Group Name
    order: 10
    icon: icon-name
  pages:
    - id: page-id
      path: /page-path
      title: Page Title
      layout: core.page@1
      blocks:
        - component: component-id@version
          props: {}
```

### 12.1 UI and Navigation Scope Note

UI and navigation manifests are out of scope for the OCC v0.0.1 schema. Capabilities MAY reference external UI and navigation manifests. The structure of those manifests is defined by other standards and is not defined by OCC.

---

## 13. MCP Tools

```yaml
mcp:
  tools:
    - id: tool-name
      description: Tool description
      inputSchema: {}
```

---

## 15. JSON-RPC Methods

This section is reserved for future use.

---

## 16. LLM Context

This section defines optional LLM hints for agent behavior.

---

## 17. Catalog Imports

```yaml
imports:
  - type: TypeName@version
  - snippet: snippetName@version
```

---

## 18. Limits and Auth

This section defines optional resource limits and authorization metadata.

---

## 19. Governance

Governance rules follow OKHAM lifecycle and versioning policies.
