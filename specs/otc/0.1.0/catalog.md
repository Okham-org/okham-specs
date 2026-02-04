# OKHAM Canonical Types Catalog

This catalog defines reusable `okham.*` OTC types intended to prevent duplicate or inconsistent core types across applications.

## When to Use Canonical Types

Use `okham.*` types when the data is cross-cutting and reusable across domains (errors, status, trace, pagination, policy decisions). These types are stable, versioned, and referenced by lint rules.

## When to Use App Types

Use app-specific namespaces (e.g., `helpdesk.*`) for domain concepts unique to the application (tickets, orders, invoices).

## Do Not Invent Common Types

Avoid creating variants such as:
- `helpdesk.error-response`
- `inventory.pagination`
- `auth.policy-decision` (use `okham.policy.decision`)

Instead, reference canonical types such as `okham.error`, `okham.pagination`, or `okham.policy.decision`.

## Catalog Index

See [index.json](index.json) for the full list of canonical types and file paths.
