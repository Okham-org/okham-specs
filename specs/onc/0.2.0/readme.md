# ONC — Open Navigation Contract

**Status**: DRAFT (v0.2.0)  
**Category**: Tooling & Format Standard

## Overview

“ONC — Open Navigation Contract: Declarative specification of navigable structure and flow,
including screens, routes, parameters, and transitions, independent of rendering technology.”

ONC defines navigable structure for applications, including screens, routes, parameters, navigation items, and transitions, without assuming any UI framework or runtime.

## Problem It Solves

Applications require a consistent, portable way to describe navigation structure without embedding navigation into UI or runtime implementations. ONC provides a neutral contract for describing navigation as data.

## Non-Goals

ONC does NOT:
- Define UI components or layouts
- Replace OUCC
- Define policies or runtime behavior
- Specify rendering technology or routing engines

## Relationships

- ONC defines navigable structure and flow.
- OUCC defines renderable components.
- OCC references navigation without embedding it.
- OPC governs access to navigation targets.
- OIC provides intent context for navigation.
