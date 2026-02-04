# Open Type Contract (OTC) Examples

**Version**: 0.1.0  
**Status**: DRAFT  
**Last Updated**: 2026-01-14

---

## Overview

> **Disclaimer**: This document is non-normative and describes recommended practices for the OKHAM ecosystem.

This document provides example Canonical Type definitions demonstrating OTC features and best practices.

---

## 1. Identifier

A universally unique identifier type.

```yaml
otc: "0.1.0"
id: "okham_core_identifier"
name: "Identifier"
description: "A universally unique identifier (UUID v4)"
tags: ["core", "identity"]

fields:
  - name: "value"
    type: "string"
    meaning: "id.uuid"
    description: "UUID v4 string representation"
    constraints:
      required: true
      format: "uuid"
    examples:
      - "550e8400-e29b-41d4-a716-446655440000"
      - "6ba7b810-9dad-11d1-80b4-00c04fd430c8"
```

---

## 2. Email Address

A validated email address type.

```yaml
otc: "0.1.0"
id: "okham_core_email-address"
name: "Email Address"
description: "A valid email address conforming to RFC 5322"
tags: ["core", "contact"]

fields:
  - name: "value"
    type: "string"
    meaning: "person.email"
    description: "Email address string"
    constraints:
      required: true
      format: "email"
      maxLength: 254
    hints:
      ui:
        inputType: "email"
        autocomplete: "email"
      i18n:
        label: "common.email.label"
        placeholder: "common.email.placeholder"
      a11y:
        description: "common.email.a11y"
      privacy:
        sensitivity: "pii"
    examples:
      - "user@example.com"
      - "john.doe@company.org"
```

---

## 3. User

A complete user account type with multiple fields.

```yaml
otc: "0.1.0"
id: "okham_core_user"
name: "User"
description: "A system user account"
tags: ["core", "identity", "account"]

fields:
  - name: "id"
    type: "string"
    meaning: "id.uuid"
    description: "Unique user identifier"
    constraints:
      required: true
      format: "uuid"
    hints:
      ui:
        hidden: true
      privacy:
        sensitivity: "internal"

  - name: "email"
    type: "string"
    meaning: "person.email"
    description: "User's primary email address"
    constraints:
      required: true
      format: "email"
      maxLength: 254
    hints:
      ui:
        inputType: "email"
        autocomplete: "email"
        order: 1
        group: "contact"
      i18n:
        label: "user.email.label"
        placeholder: "user.email.placeholder"
        help: "user.email.help"
      a11y:
        description: "user.email.a11y"
        required: true
      privacy:
        sensitivity: "pii"

  - name: "displayName"
    type: "string"
    meaning: "person.name"
    description: "User's display name"
    constraints:
      required: true
      minLength: 1
      maxLength: 100
    hints:
      ui:
        inputType: "text"
        autocomplete: "name"
        order: 2
        group: "profile"
      i18n:
        label: "user.displayName.label"
        placeholder: "user.displayName.placeholder"

  - name: "avatar"
    type: "string"
    meaning: "person.avatar"
    description: "URL to user's profile image"
    constraints:
      format: "url"
    hints:
      ui:
        inputType: "url"
        order: 3
        group: "profile"
      i18n:
        label: "user.avatar.label"

  - name: "locale"
    type: "string"
    meaning: "text.locale"
    description: "User's preferred locale"
    default: "en-US"
    constraints:
      pattern: "^[a-z]{2}(-[A-Z]{2})?$"
    hints:
      ui:
        order: 4
        group: "preferences"
      i18n:
        label: "user.locale.label"
    examples:
      - "en-US"
      - "es-ES"
      - "fr"

  - name: "active"
    type: "boolean"
    meaning: "status.active"
    description: "Whether the account is active"
    default: true
    hints:
      ui:
        order: 10
        group: "status"
      i18n:
        label: "user.active.label"

  - name: "createdAt"
    type: "string"
    meaning: "time.created"
    description: "Account creation timestamp"
    constraints:
      required: true
      format: "date-time"
    hints:
      ui:
        readonly: true
        order: 20
        group: "metadata"
      privacy:
        sensitivity: "internal"

  - name: "updatedAt"
    type: "string"
    meaning: "time.updated"
    description: "Last update timestamp"
    constraints:
      format: "date-time"
    hints:
      ui:
        readonly: true
        order: 21
        group: "metadata"
      privacy:
        sensitivity: "internal"
```

---

## 4. Address

A postal address type with geographic components.

```yaml
otc: "0.1.0"
id: "okham_core_address"
name: "Address"
description: "A postal address"
tags: ["core", "location", "contact"]

fields:
  - name: "street"
    type: "string"
    meaning: "geo.street"
    description: "Street address including number"
    constraints:
      required: true
      maxLength: 200
    hints:
      ui:
        inputType: "text"
        autocomplete: "street-address"
        order: 1
        width: "full"
      i18n:
        label: "address.street.label"
        placeholder: "address.street.placeholder"

  - name: "street2"
    type: "string"
    meaning: "geo.street"
    description: "Additional street information (apt, suite, etc.)"
    constraints:
      maxLength: 100
    hints:
      ui:
        inputType: "text"
        autocomplete: "address-line2"
        order: 2
        width: "full"
      i18n:
        label: "address.street2.label"
        placeholder: "address.street2.placeholder"

  - name: "city"
    type: "string"
    meaning: "geo.city"
    description: "City name"
    constraints:
      required: true
      maxLength: 100
    hints:
      ui:
        inputType: "text"
        autocomplete: "address-level2"
        order: 3
        width: "half"
      i18n:
        label: "address.city.label"

  - name: "region"
    type: "string"
    meaning: "geo.region"
    description: "State, province, or region"
    constraints:
      maxLength: 100
    hints:
      ui:
        inputType: "text"
        autocomplete: "address-level1"
        order: 4
        width: "quarter"
      i18n:
        label: "address.region.label"

  - name: "postalCode"
    type: "string"
    meaning: "geo.postalCode"
    description: "Postal or ZIP code"
    constraints:
      required: true
      maxLength: 20
    hints:
      ui:
        inputType: "text"
        autocomplete: "postal-code"
        order: 5
        width: "quarter"
      i18n:
        label: "address.postalCode.label"

  - name: "country"
    type: "string"
    meaning: "geo.country"
    description: "ISO 3166-1 alpha-2 country code"
    constraints:
      required: true
      pattern: "^[A-Z]{2}$"
    hints:
      ui:
        order: 6
        width: "half"
      i18n:
        label: "address.country.label"
    examples:
      - "US"
      - "GB"
      - "ES"
      - "DE"
```

---

## 5. Money

A monetary amount with currency.

```yaml
otc: "0.1.0"
id: "okham_core_money"
name: "Money"
description: "A monetary amount with currency"
tags: ["core", "financial"]

fields:
  - name: "amount"
    type: "number"
    meaning: "money.amount"
    description: "Numeric amount (use minor units for precision)"
    constraints:
      required: true
    hints:
      ui:
        inputType: "number"
        order: 1
        width: "half"
      i18n:
        label: "money.amount.label"
      a11y:
        description: "money.amount.a11y"

  - name: "currency"
    type: "string"
    meaning: "money.currency"
    description: "ISO 4217 currency code"
    constraints:
      required: true
      pattern: "^[A-Z]{3}$"
    hints:
      ui:
        order: 2
        width: "quarter"
      i18n:
        label: "money.currency.label"
    examples:
      - "USD"
      - "EUR"
      - "GBP"
      - "JPY"

  - name: "precision"
    type: "integer"
    meaning: "quantity.count"
    description: "Decimal precision (e.g., 2 for cents)"
    default: 2
    constraints:
      minimum: 0
      maximum: 8
    hints:
      ui:
        hidden: true
```

---

## 6. Timestamp

A precise moment in time.

```yaml
otc: "0.1.0"
id: "okham_core_timestamp"
name: "Timestamp"
description: "A precise moment in time (ISO 8601)"
tags: ["core", "temporal"]

fields:
  - name: "value"
    type: "string"
    meaning: "time.instant"
    description: "ISO 8601 datetime string with timezone"
    constraints:
      required: true
      format: "date-time"
    hints:
      ui:
        inputType: "datetime-local"
      i18n:
        label: "timestamp.value.label"
    examples:
      - "2026-01-14T04:30:00Z"
      - "2026-01-14T12:30:00+08:00"

  - name: "timezone"
    type: "string"
    meaning: "geo.timezone"
    description: "IANA timezone identifier"
    constraints:
      pattern: "^[A-Za-z_]+/[A-Za-z_]+$"
    hints:
      ui:
        order: 2
      i18n:
        label: "timestamp.timezone.label"
    examples:
      - "America/New_York"
      - "Europe/London"
      - "Asia/Tokyo"
```

---

## 7. Coordinate

A geographic coordinate (latitude/longitude).

```yaml
otc: "0.1.0"
id: "okham_geo_coordinate"
name: "Coordinate"
description: "A geographic coordinate point"
tags: ["geo", "location"]

fields:
  - name: "latitude"
    type: "number"
    meaning: "geo.latitude"
    description: "Latitude in decimal degrees"
    constraints:
      required: true
      minimum: -90
      maximum: 90
    hints:
      ui:
        inputType: "number"
        order: 1
        width: "half"
      i18n:
        label: "coordinate.latitude.label"
        unit: "coordinate.degrees"
    examples:
      - 40.7128
      - -33.8688
      - 51.5074

  - name: "longitude"
    type: "number"
    meaning: "geo.longitude"
    description: "Longitude in decimal degrees"
    constraints:
      required: true
      minimum: -180
      maximum: 180
    hints:
      ui:
        inputType: "number"
        order: 2
        width: "half"
      i18n:
        label: "coordinate.longitude.label"
        unit: "coordinate.degrees"
    examples:
      - -74.0060
      - 151.2093
      - -0.1278

  - name: "altitude"
    type: "number"
    meaning: "geo.altitude"
    description: "Altitude in meters above sea level"
    constraints:
      minimum: -500
      maximum: 10000
    hints:
      ui:
        inputType: "number"
        order: 3
        width: "half"
      i18n:
        label: "coordinate.altitude.label"
        unit: "coordinate.meters"
```

---

## 8. API Key

A secure API key credential.

```yaml
otc: "0.1.0"
id: "okham.security.api-key"
name: "API Key"
description: "A secure API key for authentication"
tags: ["security", "credential"]

fields:
  - name: "id"
    type: "string"
    meaning: "id.uuid"
    description: "Unique key identifier"
    constraints:
      required: true
      format: "uuid"
    hints:
      ui:
        readonly: true
      privacy:
        sensitivity: "internal"

  - name: "name"
    type: "string"
    meaning: "meta.label"
    description: "Human-readable key name"
    constraints:
      required: true
      minLength: 1
      maxLength: 100
    hints:
      ui:
        inputType: "text"
        order: 1
      i18n:
        label: "apiKey.name.label"
        placeholder: "apiKey.name.placeholder"

  - name: "key"
    type: "string"
    meaning: "security.apiKey"
    description: "The API key value (shown only once)"
    constraints:
      required: true
      minLength: 32
    hints:
      ui:
        masked: true
        readonly: true
      privacy:
        sensitivity: "secret"
        mask: true
        audit: true

  - name: "scopes"
    type: "array"
    meaning: "meta.tags"
    description: "Authorized scopes for this key"
    constraints:
      minItems: 1
    hints:
      ui:
        order: 3
      i18n:
        label: "apiKey.scopes.label"
    examples:
      - ["read", "write"]
      - ["admin"]

  - name: "expiresAt"
    type: "string"
    meaning: "time.expires"
    description: "Key expiration timestamp"
    constraints:
      format: "date-time"
    hints:
      ui:
        inputType: "datetime-local"
        order: 4
      i18n:
        label: "apiKey.expiresAt.label"

  - name: "createdAt"
    type: "string"
    meaning: "time.created"
    description: "Key creation timestamp"
    constraints:
      required: true
      format: "date-time"
    hints:
      ui:
        readonly: true
      privacy:
        sensitivity: "internal"

  - name: "lastUsedAt"
    type: "string"
    meaning: "time.updated"
    description: "Last usage timestamp"
    constraints:
      format: "date-time"
    hints:
      ui:
        readonly: true
      privacy:
        sensitivity: "internal"
```

---

## 9. File Metadata

Metadata for an uploaded file.

```yaml
otc: "0.1.0"
id: "okham.media.file-metadata"
name: "File Metadata"
description: "Metadata for an uploaded file"
tags: ["media", "storage"]

fields:
  - name: "id"
    type: "string"
    meaning: "id.uuid"
    description: "Unique file identifier"
    constraints:
      required: true
      format: "uuid"
    hints:
      ui:
        hidden: true

  - name: "name"
    type: "string"
    meaning: "meta.label"
    description: "Original filename"
    constraints:
      required: true
      maxLength: 255
    hints:
      ui:
        inputType: "text"
        order: 1
      i18n:
        label: "file.name.label"

  - name: "mimeType"
    type: "string"
    meaning: "media.mimeType"
    description: "MIME type of the file"
    constraints:
      required: true
      pattern: "^[a-z]+/[a-z0-9.+-]+$"
    hints:
      ui:
        readonly: true
        order: 2
      i18n:
        label: "file.mimeType.label"
    examples:
      - "image/png"
      - "application/pdf"
      - "text/plain"

  - name: "size"
    type: "integer"
    meaning: "media.size"
    description: "File size in bytes"
    constraints:
      required: true
      minimum: 0
    hints:
      ui:
        readonly: true
        order: 3
      i18n:
        label: "file.size.label"
        unit: "file.bytes"

  - name: "checksum"
    type: "string"
    meaning: "media.checksum"
    description: "SHA-256 hash of file contents"
    constraints:
      pattern: "^[a-f0-9]{64}$"
    hints:
      ui:
        readonly: true
        hidden: true
      privacy:
        sensitivity: "internal"

  - name: "url"
    type: "string"
    meaning: "text.url"
    description: "URL to access the file"
    constraints:
      format: "url"
    hints:
      ui:
        readonly: true
        order: 4
      i18n:
        label: "file.url.label"

  - name: "uploadedAt"
    type: "string"
    meaning: "time.created"
    description: "Upload timestamp"
    constraints:
      required: true
      format: "date-time"
    hints:
      ui:
        readonly: true
      privacy:
        sensitivity: "internal"
```

---

## 10. Pagination

Pagination parameters for list queries.

```yaml
otc: "0.1.0"
id: "okham_core_pagination"
name: "Pagination"
description: "Pagination parameters for list queries"
tags: ["core", "query"]

fields:
  - name: "page"
    type: "integer"
    meaning: "quantity.count"
    description: "Current page number (1-indexed)"
    default: 1
    constraints:
      minimum: 1
    hints:
      ui:
        inputType: "number"
        order: 1
      i18n:
        label: "pagination.page.label"

  - name: "pageSize"
    type: "integer"
    meaning: "quantity.count"
    description: "Number of items per page"
    default: 20
    constraints:
      minimum: 1
      maximum: 100
    hints:
      ui:
        inputType: "number"
        order: 2
      i18n:
        label: "pagination.pageSize.label"

  - name: "totalItems"
    type: "integer"
    meaning: "quantity.count"
    description: "Total number of items"
    constraints:
      minimum: 0
    hints:
      ui:
        readonly: true
        order: 3
      i18n:
        label: "pagination.totalItems.label"

  - name: "totalPages"
    type: "integer"
    meaning: "quantity.count"
    description: "Total number of pages"
    constraints:
      minimum: 0
    hints:
      ui:
        readonly: true
        order: 4
      i18n:
        label: "pagination.totalPages.label"
```

---

## Type Inheritance Example

Demonstrating type extension with an abstract base type.

### Base: Person (Abstract)

```yaml
otc: "0.1.0"
id: "okham_core_person"
name: "Person"
description: "Abstract base type for person entities"
abstract: true
tags: ["core", "identity"]

fields:
  - name: "givenName"
    type: "string"
    meaning: "person.givenName"
    description: "First/given name"
    constraints:
      required: true
      maxLength: 100
    hints:
      ui:
        inputType: "text"
        autocomplete: "given-name"
        order: 1
      i18n:
        label: "person.givenName.label"

  - name: "familyName"
    type: "string"
    meaning: "person.familyName"
    description: "Last/family name"
    constraints:
      required: true
      maxLength: 100
    hints:
      ui:
        inputType: "text"
        autocomplete: "family-name"
        order: 2
      i18n:
        label: "person.familyName.label"

  - name: "email"
    type: "string"
    meaning: "person.email"
    description: "Email address"
    constraints:
      format: "email"
    hints:
      ui:
        inputType: "email"
        order: 3
      i18n:
        label: "person.email.label"
      privacy:
        sensitivity: "pii"
```

### Extended: Employee

```yaml
otc: "0.1.0"
id: "acme.hr.employee"
name: "Employee"
description: "An employee in the organization"
extends: "okham_core_person"
tags: ["hr", "identity"]

fields:
  # Inherits givenName, familyName, email from okham_core_person

  - name: "employeeId"
    type: "string"
    meaning: "id.internal"
    description: "Internal employee identifier"
    constraints:
      required: true
      pattern: "^EMP-[0-9]{6}$"
    hints:
      ui:
        readonly: true
        order: 0
      i18n:
        label: "employee.id.label"
    examples:
      - "EMP-000001"
      - "EMP-123456"

  - name: "department"
    type: "string"
    meaning: "org.name"
    description: "Department name"
    constraints:
      required: true
    hints:
      ui:
        order: 10
      i18n:
        label: "employee.department.label"

  - name: "hireDate"
    type: "string"
    meaning: "time.date"
    description: "Date of hire"
    constraints:
      required: true
      format: "date"
    hints:
      ui:
        inputType: "date"
        order: 11
      i18n:
        label: "employee.hireDate.label"

  - name: "salary"
    type: "$ref:okham_core_money"
    meaning: "money.amount"
    description: "Annual salary"
    hints:
      ui:
        order: 12
      i18n:
        label: "employee.salary.label"
      privacy:
        sensitivity: "secret"
        mask: true
```

---

## References

- [CTC Specification](SPECIFICATION.md)
- [CTC Schema](SCHEMA.md)
- [CTC README](README.md)
