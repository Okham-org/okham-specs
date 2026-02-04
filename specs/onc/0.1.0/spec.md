# OKHAM ONC — Open Navigation Contract
- Contract: ONC
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/onc/0.1.0/schema.json

## 1. Scope

ONC defines a declarative navigation manifest that describes:
- screens
- routes
- route parameters
- navigation items
- transitions
- guards (policy references only)

## 2. Navigation Manifest

A Navigation Manifest MUST be a JSON object that includes:
- `navigation.id` (string, REQUIRED): Unique manifest identifier.
- `navigation.version` (string, REQUIRED): SemVer for the manifest.
- `screens` (array, REQUIRED): Screen definitions.
- `routes` (array, REQUIRED): Route definitions.
- `nav.items` (array, REQUIRED): Navigation item definitions.
- `guards` (array, OPTIONAL): Guard definitions.

## 3. Screen

A Screen MUST include:
- `id` (string, REQUIRED): Screen identifier.
- `title` (string, REQUIRED): Human-readable title.
- `componentRef` (string, REQUIRED): OUCC component reference.

A Screen MAY include:
- `intent` (string, OPTIONAL): OIC intent reference for context.
- `description` (string, OPTIONAL).

## 4. Route

A Route MUST include:
- `path` (string, REQUIRED): Route path (e.g., `/tickets/:ticketId`).
- `screenId` (string, REQUIRED): Reference to a Screen `id`.

A Route MAY include:
- `params` (array, OPTIONAL): Route parameter definitions.
- `guards` (array, OPTIONAL): Guard references by `id`.

## 5. Route Parameters

A Route Parameter MUST include:
- `name` (string, REQUIRED): Parameter name.
- `type` (string, REQUIRED): OTC type reference.

## 6. Navigation Items

A Navigation Item MUST include:
- `id` (string, REQUIRED): Navigation item identifier.
- `label` (string, REQUIRED): Display label.
- `route` (string, REQUIRED): Route `path` reference.

A Navigation Item MAY include:
- `guards` (array, OPTIONAL): Guard references by `id`.
- `intent` (string, OPTIONAL): OIC intent reference.

## 7. Guards

A Guard MUST include:
- `id` (string, REQUIRED): Guard identifier.
- `policyRef` (string, REQUIRED): OPC policy reference.

## 8. Compliance

A document is ONC-compliant if it satisfies all REQUIRED fields and all references resolve to defined entities within the manifest.

## 9. Out of Scope

ONC does NOT define:
- UI layouts or components (OUCC covers components)
- UI runtime behavior or routing engines
- Policy semantics or enforcement (OPC covers policy)
- Authorization or identity models
