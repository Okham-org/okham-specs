# UI Components Examples

## Overview

> **Disclaimer**: This document is non-normative and describes recommended practices for the OKHAM ecosystem.

This document provides practical examples of OUCC component manifests with rendering profiles and artifact references.

## Status: DRAFT (v0.1.0)

These examples demonstrate OUCC v0.1.0 specifications with multi-engine rendering profiles.

---

## Example 1: Card Component with Multiple Profiles

A layout component that supports both Go templ and Thymeleaf rendering engines.

**File**: `catalog/components/card/component.yaml`

```yaml
component: "1.0.0"
id: "okham_ui_card"
name: "Card"
description: "Content container with elevation, padding, and optional header/footer. Used for grouping related information in a visually distinct block."
version: "1.0.0"
status: "stable"
category: "layout"
tags:
  - "layout"
  - "container"
  - "content"
  - "mvp"

props:
  - name: "title"
    type: "string"
    required: false
    description: "Card header title"

  - name: "subtitle"
    type: "string"
    required: false
    description: "Card header subtitle"

  - name: "variant"
    type: "string"
    required: false
    description: "Visual style variant"
    default: "elevated"
    enum:
      - "elevated"
      - "outlined"
      - "filled"
      - "flat"

  - name: "padding"
    type: "string"
    required: false
    description: "Internal padding size"
    default: "medium"
    enum:
      - "none"
      - "small"
      - "medium"
      - "large"

slots:
  - name: "header"
    description: "Custom header content (replaces title/subtitle)"
    accepts: ["okham_ui_heading", "okham_ui_text", "okham_ui_*"]

  - name: "content"
    description: "Main card body content"
    accepts: ["okham_ui_*"]

  - name: "actions"
    description: "Card footer actions"
    accepts: ["okham_ui_button", "okham_ui_link", "okham_ui_*"]

events:
  - name: "onClick"
    description: "Fired when interactive card is clicked"
    payload:
      type: "object"
      properties:
        event:
          type: "object"
          description: "Native click event"

profiles:
  - engine: templ
    artifact:
      kind: file
      path: templ/card.templ
      sha256: "3a5b8c7d9e1f2a4b6c8d0e2f4a6b8c0d2e4f6a8b0c2d4e6f8a0b2c4d6e8f0a2b"

  - engine: thymeleaf
    artifact:
      kind: file
      path: thymeleaf/card.html
      sha256: "7d9e1f2a4b6c8d0e2f4a6b8c0d2e4f6a8b0c2d4e6f8a0b2c4d6e8f0a2b3c5d"

i18n:
  keys:
    - "card.title.label"
    - "card.subtitle.label"
    - "card.loading"
    - "card.clickToView"

a11y:
  role: "article"
  roleInteractive: "button"
  attributes:
    - name: "aria-labelledby"
      source: "titleId"
  keyboard:
    - key: "Enter"
      action: "activate"
      condition: "props.interactive || props.href"
```

**Artifact Files:**

`catalog/components/card/templ/card.templ`:
```templ
// Canonical Component: Card
// Standard: OUCC v1.0.0 / templ Rendering Profile
// Component ID: okham_ui_card

package templ

templ Card(title string, subtitle string, variant string, padding string) {
    <div class={ cardClass(variant, padding) } role="article">
        if title != "" {
            <div class="ui-card__header">
                <h3 class="ui-card__title">{ title }</h3>
                if subtitle != "" {
                    <p class="ui-card__subtitle">{ subtitle }</p>
                }
            </div>
        }
        <div class="ui-card__content">
            { children... }
        </div>
    </div>
}

func cardClass(variant string, padding string) string {
    base := "ui-card"
    if variant != "" {
        base += " ui-card--" + variant
    }
    if padding != "" {
        base += " ui-card--padding-" + padding
    }
    return base
}
```

`catalog/components/card/thymeleaf/card.html`:
```html
<!-- Canonical Component: Card -->
<!-- Standard: OUCC v1.0.0 / Thymeleaf Rendering Profile -->
<!-- Component ID: okham_ui_card -->

<div th:class="|ui-card ui-card--${variant ?: 'elevated'} ui-card--padding-${padding ?: 'medium'}|" role="article">
    <div th:if="${title != null and !title.isEmpty()}" class="ui-card__header">
        <h3 class="ui-card__title" th:text="${title}">Card Title</h3>
        <p th:if="${subtitle != null and !subtitle.isEmpty()}"
           class="ui-card__subtitle"
           th:text="${subtitle}">Card Subtitle</p>
    </div>
    <div class="ui-card__content">
        <!-- Slot: content -->
        <th:block th:replace="${content}"></th:block>
    </div>
</div>
```

---

## Example 2: Hero Component (Single Profile)

A layout component with one rendering engine (templ only).

**File**: `catalog/components/hero/component.yaml`

```yaml
component: "1.0.0"
id: "okham_ui_hero"
name: "Hero"
description: "Full-width hero banner section for page headers, landing pages, and prominent call-to-action areas."
version: "1.0.0"
status: "stable"
category: "layout"
tags:
  - "layout"
  - "banner"
  - "header"
  - "landing"
  - "mvp"

props:
  - name: "title"
    type: "string"
    required: true
    description: "Main hero title/headline"

  - name: "subtitle"
    type: "string"
    required: false
    description: "Secondary text below the title"

  - name: "variant"
    type: "string"
    required: false
    description: "Visual style variant"
    default: "default"
    enum:
      - "default"
      - "dark"
      - "light"
      - "centered"
      - "gradient"

  - name: "size"
    type: "string"
    required: false
    description: "Hero section height"
    default: "medium"
    enum:
      - "small"
      - "medium"
      - "large"
      - "fullscreen"

slots:
  - name: "content"
    description: "Additional content below title/subtitle"
    accepts: ["okham_ui_text", "okham_ui_button", "okham_ui_*"]

  - name: "actions"
    description: "Action buttons"
    accepts: ["okham_ui_button", "okham_ui_link"]

profiles:
  - engine: templ
    artifact:
      kind: file
      path: templ/hero.templ
      sha256: "9e1f2a4b6c8d0e2f4a6b8c0d2e4f6a8b0c2d4e6f8a0b2c4d6e8f0a2b3c5d7e9f"

i18n:
  keys:
    - "hero.title.label"
    - "hero.subtitle.label"
    - "hero.primaryAction.label"
    - "hero.secondaryAction.label"

a11y:
  role: "banner"
  attributes:
    - name: "aria-labelledby"
      source: "titleId"
  keyboard:
    - key: "Tab"
      action: "focusActions"
      description: "Navigate to action buttons"
  focus:
    visible: true
    trapped: false
```

**Artifact File:**

`catalog/components/hero/templ/hero.templ`:
```templ
// Canonical Component: Hero
// Standard: OUCC v1.0.0 / templ Rendering Profile
// Component ID: okham_ui_hero

package templ

templ Hero(title string, subtitle string, variant string, size string) {
    <section class={ heroClass(variant, size) } role="banner">
        <div class="ui-hero__container">
            <h1 class="ui-hero__title">{ title }</h1>
            if subtitle != "" {
                <p class="ui-hero__subtitle">{ subtitle }</p>
            }
            <div class="ui-hero__content">
                { children... }
            </div>
        </div>
    </section>
}

func heroClass(variant string, size string) string {
    base := "ui-hero"
    if variant != "" {
        base += " ui-hero--" + variant
    }
    if size != "" {
        base += " ui-hero--" + size
    }
    return base
}
```

---

## Example 3: Button Component (Multiple Profiles with Future Extensions)

A form component demonstrating artifact kind extensibility.

**File**: `catalog/components/button/component.yaml`

```yaml
component: "1.0.0"
id: "okham_ui_button"
name: "Button"
description: "Interactive button component with variants, sizes, and loading states."
version: "1.1.0"
status: "stable"
category: "form"
tags:
  - "form"
  - "interactive"
  - "action"
  - "mvp"

props:
  - name: "label"
    type: "string"
    required: true
    description: "Button text label"

  - name: "variant"
    type: "string"
    required: false
    default: "primary"
    enum:
      - "primary"
      - "secondary"
      - "ghost"
      - "danger"

  - name: "size"
    type: "string"
    required: false
    default: "medium"
    enum:
      - "small"
      - "medium"
      - "large"

  - name: "disabled"
    type: "boolean"
    required: false
    default: false

  - name: "loading"
    type: "boolean"
    required: false
    default: false

events:
  - name: "onClick"
    description: "Fired when button is clicked"
    payload:
      type: "object"

profiles:
  # File-based artifact (current)
  - engine: templ
    artifact:
      kind: file
      path: templ/button.templ
      sha256: "1a2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b"

  # File-based artifact (current)
  - engine: thymeleaf
    artifact:
      kind: file
      path: thymeleaf/button.html
      sha256: "2b3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c"

  # Future: URI-based artifact (reserved, not yet supported)
  # - engine: mustache
  #   artifact:
  #     kind: uri
  #     uri: "https://cdn.example.com/templates/button.mustache"
  #     sha256: "3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c4d"

a11y:
  role: "button"
  attributes:
    - name: "aria-disabled"
      source: "props.disabled"
    - name: "aria-busy"
      source: "props.loading"
```

---

## Example 4: Migration from Inline Template (Discouraged)

**Before (Inline - DISCOURAGED):**

```yaml
# OLD APPROACH - DO NOT USE
component: "1.0.0"
id: "okham_ui_alert"
name: "Alert"
version: "0.9.0"

template: |
  <div class="alert alert-{{ variant }}">
    <span class="alert-icon">{{ icon }}</span>
    <p class="alert-message">{{ message }}</p>
  </div>
```

**After (Profiles - RECOMMENDED):**

```yaml
component: "1.0.0"
id: "okham_ui_alert"
name: "Alert"
description: "Status messages and notifications with variants"
version: "1.0.0"
status: "stable"
category: "feedback"

props:
  - name: "message"
    type: "string"
    required: true
  - name: "variant"
    type: "string"
    default: "info"
    enum: ["info", "success", "warning", "error"]

profiles:
  - engine: templ
    artifact:
      kind: file
      path: templ/alert.templ
      sha256: "4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0f1a2b3c4d5e"
```

---

## Validation Example

Components can be validated against the OUCC schema:

```bash
# Validate component manifest
jsonschema -i catalog/components/card/component.yaml \
           docs/standards/oucc/schema/v1/component.schema.json

# Verify artifact integrity
sha256sum catalog/components/card/templ/card.templ
# Compare with artifact.sha256 in component.yaml
```

---

## Related Documents

- [UI Components Specification](SPECIFICATION.md) - Full OUCC standard
- [UI Components Schema](SCHEMA.md) - JSON Schema documentation
- [UI Components README](README.md) - Overview and rationale
- [UI Components Governance](GOVERNANCE.md) - Standards governance
