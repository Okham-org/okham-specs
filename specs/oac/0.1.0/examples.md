# Open Accessibility Contract (OAC) Examples

**Version**: 0.1.0  
**Status**: DRAFT  
**Last Updated**: 2026-01-17

---

## Overview

This document provides non-normative examples demonstrating OAC accessibility obligations, evidence collection, and semantic role declarations.

---

## 1. Button Component with Keyboard Navigation

A primary button with complete accessibility obligations and evidence.

```yaml
oac: "0.1.0"
component: "button-primary"

obligations:
  - id: "keyboard-navigation"
    target: "button-primary"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "2.1.1"
        description: "All functionality available from keyboard"
        level: "A"
      - criterion: "2.1.2"
        description: "No keyboard trap"
        level: "A"
    priority: "critical"
    evidence:
      - type: "automated"
        check: "focusable-with-keyboard"
      - type: "manual"
        check: "keyboard-activation-verified"

  - id: "color-contrast"
    target: "button-primary"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "1.4.3"
        description: "Contrast ratio at least 4.5:1"
        level: "AA"
    priority: "high"
    evidence:
      - type: "automated"
        check: "contrast-ratio-4.5-to-1"

  - id: "accessible-name"
    target: "button-primary"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "4.1.2"
        description: "Name, role, value available"
        level: "A"
    priority: "critical"
    evidence:
      - type: "automated"
        check: "aria-label-or-text-content-present"

semanticRole:
  role: "button"
  component: "button-primary"
  keyboardInteractions:
    - key: "Enter"
      action: "activate"
      condition: "when-focused"
    - key: "Space"
      action: "activate"
      condition: "when-focused"
  screenReaderAnnouncement: "Submit button"
  focusManagement:
    focusable: true
    tabIndex: 0
  states: ["aria-pressed", "aria-disabled"]
  properties: ["aria-label", "aria-describedby"]

evidence:
  - type: "automated"
    check: "contrast-ratio-4.5-to-1"
    result: "pass"
    measured: "4.8:1"
    timestamp: "2026-01-17T10:30:00Z"
    verifier: "axe-core@4.7.0"
    obligationId: "color-contrast"
  
  - type: "automated"
    check: "focusable-with-keyboard"
    result: "pass"
    timestamp: "2026-01-17T10:30:00Z"
    verifier: "axe-core@4.7.0"
    obligationId: "keyboard-navigation"
  
  - type: "manual"
    check: "keyboard-activation-verified"
    result: "pass"
    timestamp: "2026-01-17T11:00:00Z"
    verifier: "manual-tester-alice"
    notes: "Verified Enter and Space keys activate button correctly"
    obligationId: "keyboard-navigation"
```

---

## 2. Modal Dialog with Focus Management

A modal dialog demonstrating complex focus management and keyboard interactions.

```yaml
oac: "0.1.0"
component: "modal-dialog"

obligations:
  - id: "focus-trap"
    target: "modal-dialog"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "2.4.3"
        description: "Focus order preserves meaning and operability"
        level: "A"
    priority: "critical"
    evidence:
      - type: "automated"
        check: "focus-trapped-within-modal"
      - type: "manual"
        check: "tab-order-logical"

  - id: "keyboard-close"
    target: "modal-dialog"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "2.1.1"
        description: "All functionality available from keyboard"
        level: "A"
    priority: "critical"
    evidence:
      - type: "automated"
        check: "escape-key-closes-modal"
      - type: "manual"
        check: "close-button-keyboard-accessible"

  - id: "screen-reader-announcement"
    target: "modal-dialog"
    standard: "ARIA 1.2"
    requirements:
      - criterion: "dialog-role"
        description: "Dialog role and label announced"
    priority: "high"
    evidence:
      - type: "manual"
        check: "screen-reader-announces-dialog"

  - id: "focus-restoration"
    target: "modal-dialog"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "2.4.3"
        description: "Focus returns to trigger element on close"
        level: "A"
    priority: "high"
    evidence:
      - type: "automated"
        check: "focus-restored-on-close"

semanticRole:
  role: "dialog"
  component: "modal-dialog"
  keyboardInteractions:
    - key: "Escape"
      action: "close"
      condition: "when-modal-open"
    - key: "Tab"
      action: "navigate-next"
      condition: "when-focused"
    - key: "Shift+Tab"
      action: "navigate-previous"
      condition: "when-focused"
  screenReaderAnnouncement: "Dialog, Confirm action, press Escape to close"
  focusManagement:
    focusable: true
    trapFocus: true
    focusOnMount: true
    restoreFocusOnUnmount: true
  states: ["aria-modal", "aria-labelledby", "aria-describedby"]
  properties: ["aria-label"]

evidence:
  - type: "automated"
    check: "focus-trapped-within-modal"
    result: "pass"
    timestamp: "2026-01-17T10:45:00Z"
    verifier: "jest-axe@8.0.0"
    obligationId: "focus-trap"
  
  - type: "automated"
    check: "escape-key-closes-modal"
    result: "pass"
    timestamp: "2026-01-17T10:45:00Z"
    verifier: "jest-axe@8.0.0"
    obligationId: "keyboard-close"
  
  - type: "manual"
    check: "screen-reader-announces-dialog"
    result: "pass"
    timestamp: "2026-01-17T11:15:00Z"
    verifier: "manual-tester-bob"
    notes: "NVDA announces 'Dialog, Confirm action' on open"
    obligationId: "screen-reader-announcement"
  
  - type: "automated"
    check: "focus-restored-on-close"
    result: "pass"
    timestamp: "2026-01-17T10:45:00Z"
    verifier: "jest-axe@8.0.0"
    obligationId: "focus-restoration"
```

---

## 3. Form Input with Validation and Error Handling

A text input field with comprehensive accessibility for validation and error states.

```yaml
oac: "0.1.0"
component: "text-input-email"

obligations:
  - id: "label-association"
    target: "text-input-email"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "1.3.1"
        description: "Info and relationships programmatically determined"
        level: "A"
      - criterion: "3.3.2"
        description: "Labels or instructions provided"
        level: "A"
    priority: "critical"
    evidence:
      - type: "automated"
        check: "label-associated-with-input"
      - type: "automated"
        check: "aria-labelledby-or-label-present"

  - id: "error-identification"
    target: "text-input-email"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "3.3.1"
        description: "Error identification"
        level: "A"
      - criterion: "3.3.3"
        description: "Error suggestion"
        level: "AA"
    priority: "high"
    evidence:
      - type: "automated"
        check: "aria-invalid-on-error"
      - type: "automated"
        check: "aria-describedby-links-to-error"
      - type: "manual"
        check: "error-message-clear-and-helpful"

  - id: "keyboard-input"
    target: "text-input-email"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "2.1.1"
        description: "Keyboard accessible"
        level: "A"
    priority: "critical"
    evidence:
      - type: "automated"
        check: "input-focusable"

semanticRole:
  role: "textbox"
  component: "text-input-email"
  keyboardInteractions:
    - key: "Tab"
      action: "navigate-next"
      condition: "when-focused"
    - key: "Shift+Tab"
      action: "navigate-previous"
      condition: "when-focused"
  screenReaderAnnouncement: "Email address, edit text"
  focusManagement:
    focusable: true
    tabIndex: 0
  states: ["aria-invalid", "aria-required"]
  properties: ["aria-label", "aria-labelledby", "aria-describedby"]

evidence:
  - type: "automated"
    check: "label-associated-with-input"
    result: "pass"
    timestamp: "2026-01-17T12:00:00Z"
    verifier: "lighthouse@10.0.0"
    obligationId: "label-association"
  
  - type: "automated"
    check: "aria-invalid-on-error"
    result: "pass"
    timestamp: "2026-01-17T12:00:00Z"
    verifier: "lighthouse@10.0.0"
    notes: "aria-invalid='true' set when validation fails"
    obligationId: "error-identification"
  
  - type: "automated"
    check: "aria-describedby-links-to-error"
    result: "pass"
    timestamp: "2026-01-17T12:00:00Z"
    verifier: "lighthouse@10.0.0"
    notes: "aria-describedby references error message element"
    obligationId: "error-identification"
  
  - type: "manual"
    check: "error-message-clear-and-helpful"
    result: "pass"
    timestamp: "2026-01-17T12:30:00Z"
    verifier: "manual-tester-carol"
    notes: "Error message: 'Please enter a valid email address (e.g., user@example.com)'"
    obligationId: "error-identification"
```

---

## 4. Navigation Menu with ARIA

A navigation menu demonstrating ARIA roles and keyboard navigation patterns.

```yaml
oac: "0.1.0"
component: "navigation-menu"

obligations:
  - id: "navigation-landmark"
    target: "navigation-menu"
    standard: "ARIA 1.2"
    requirements:
      - criterion: "navigation-role"
        description: "Navigation landmark identified"
    priority: "high"
    evidence:
      - type: "automated"
        check: "navigation-role-present"

  - id: "keyboard-navigation"
    target: "navigation-menu"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "2.1.1"
        description: "Keyboard accessible"
        level: "A"
    priority: "critical"
    evidence:
      - type: "automated"
        check: "all-links-focusable"
      - type: "manual"
        check: "arrow-key-navigation-works"

  - id: "current-page-indication"
    target: "navigation-menu"
    standard: "ARIA 1.2"
    requirements:
      - criterion: "aria-current"
        description: "Current page indicated with aria-current"
    priority: "medium"
    evidence:
      - type: "automated"
        check: "aria-current-page-present"

semanticRole:
  role: "navigation"
  component: "navigation-menu"
  keyboardInteractions:
    - key: "Tab"
      action: "navigate-next"
      condition: "when-focused"
    - key: "Shift+Tab"
      action: "navigate-previous"
      condition: "when-focused"
    - key: "ArrowDown"
      action: "navigate-next"
      condition: "when-focused"
    - key: "ArrowUp"
      action: "navigate-previous"
      condition: "when-focused"
  screenReaderAnnouncement: "Navigation menu, 5 items"
  focusManagement:
    focusable: false
  properties: ["aria-label", "aria-current"]

evidence:
  - type: "automated"
    check: "navigation-role-present"
    result: "pass"
    timestamp: "2026-01-17T13:00:00Z"
    verifier: "axe-core@4.7.0"
    obligationId: "navigation-landmark"
  
  - type: "automated"
    check: "all-links-focusable"
    result: "pass"
    timestamp: "2026-01-17T13:00:00Z"
    verifier: "axe-core@4.7.0"
    obligationId: "keyboard-navigation"
  
  - type: "manual"
    check: "arrow-key-navigation-works"
    result: "pass"
    timestamp: "2026-01-17T13:30:00Z"
    verifier: "manual-tester-dave"
    notes: "Arrow keys navigate between menu items as expected"
    obligationId: "keyboard-navigation"
  
  - type: "automated"
    check: "aria-current-page-present"
    result: "pass"
    timestamp: "2026-01-17T13:00:00Z"
    verifier: "axe-core@4.7.0"
    notes: "Current page link has aria-current='page'"
    obligationId: "current-page-indication"
```

---

## 5. Data Table with Screen Reader Support

A data table demonstrating proper semantic structure for screen readers.

```yaml
oac: "0.1.0"
component: "data-table-users"

obligations:
  - id: "table-structure"
    target: "data-table-users"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "1.3.1"
        description: "Info and relationships programmatically determined"
        level: "A"
    priority: "critical"
    evidence:
      - type: "automated"
        check: "table-has-headers"
      - type: "automated"
        check: "headers-associated-with-cells"

  - id: "table-caption"
    target: "data-table-users"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "1.3.1"
        description: "Table purpose identified"
        level: "A"
    priority: "high"
    evidence:
      - type: "automated"
        check: "caption-or-aria-label-present"

  - id: "sortable-columns"
    target: "data-table-users"
    standard: "ARIA 1.2"
    requirements:
      - criterion: "aria-sort"
        description: "Sort state communicated"
    priority: "medium"
    evidence:
      - type: "automated"
        check: "aria-sort-on-sortable-headers"
      - type: "manual"
        check: "sort-state-announced"

semanticRole:
  role: "table"
  component: "data-table-users"
  keyboardInteractions:
    - key: "Tab"
      action: "navigate-next"
      condition: "when-focused"
    - key: "Enter"
      action: "activate"
      condition: "when-header-focused"
  screenReaderAnnouncement: "User list table, 3 columns, 10 rows"
  focusManagement:
    focusable: false
  properties: ["aria-label", "aria-describedby", "aria-sort"]

evidence:
  - type: "automated"
    check: "table-has-headers"
    result: "pass"
    timestamp: "2026-01-17T14:00:00Z"
    verifier: "axe-core@4.7.0"
    obligationId: "table-structure"
  
  - type: "automated"
    check: "headers-associated-with-cells"
    result: "pass"
    timestamp: "2026-01-17T14:00:00Z"
    verifier: "axe-core@4.7.0"
    notes: "All cells properly associated with headers via scope or headers attribute"
    obligationId: "table-structure"
  
  - type: "automated"
    check: "caption-or-aria-label-present"
    result: "pass"
    measured: "aria-label='User list'"
    timestamp: "2026-01-17T14:00:00Z"
    verifier: "axe-core@4.7.0"
    obligationId: "table-caption"
  
  - type: "automated"
    check: "aria-sort-on-sortable-headers"
    result: "pass"
    timestamp: "2026-01-17T14:00:00Z"
    verifier: "axe-core@4.7.0"
    obligationId: "sortable-columns"
  
  - type: "manual"
    check: "sort-state-announced"
    result: "pass"
    timestamp: "2026-01-17T14:30:00Z"
    verifier: "manual-tester-eve"
    notes: "JAWS announces 'Name column, sorted ascending' correctly"
    obligationId: "sortable-columns"
```

---

## 6. Exemption Example: Decorative Image

An example showing a documented exemption for a decorative image.

```yaml
oac: "0.1.0"
component: "decorative-background-image"

obligations:
  - id: "alt-text-requirement"
    target: "decorative-background-image"
    standard: "WCAG 2.1 AA"
    requirements:
      - criterion: "1.1.1"
        description: "Non-text content has text alternative"
        level: "A"
    priority: "high"
    exemptions:
      - criterion: "1.1.1"
        justification: "Image is purely decorative and does not convey information. Using empty alt text (alt='') as per WCAG guidelines for decorative images."
        approvedBy: "accessibility-team-lead"
        expiresAt: null

semanticRole:
  role: "img"
  component: "decorative-background-image"
  properties: ["alt"]
  metadata:
    decorative: true

evidence:
  - type: "automated"
    check: "alt-attribute-present"
    result: "pass"
    measured: "alt=''"
    timestamp: "2026-01-17T15:00:00Z"
    verifier: "axe-core@4.7.0"
    notes: "Empty alt text correctly indicates decorative image"
    obligationId: "alt-text-requirement"
  
  - type: "documentation"
    check: "exemption-documented"
    result: "pass"
    timestamp: "2026-01-17T15:00:00Z"
    verifier: "accessibility-team-lead"
    notes: "Exemption approved and documented in accessibility audit"
    obligationId: "alt-text-requirement"
```

---

## 7. Integration with OUCC Component

Example showing OAC integrated within an OUCC component definition.

```yaml
# OUCC Component with OAC
oucc: "0.1.0"
id: "alert-banner"
name: "Alert Banner"
description: "Accessible alert banner component"

# ... other OUCC fields ...

accessibility:
  oac: "0.1.0"
  obligations:
    - id: "alert-role"
      target: "alert-banner"
      standard: "ARIA 1.2"
      requirements:
        - criterion: "alert-role"
          description: "Alert role for important messages"
      priority: "critical"
      evidence:
        - type: "automated"
          check: "role-alert-present"
    
    - id: "live-region"
      target: "alert-banner"
      standard: "ARIA 1.2"
      requirements:
        - criterion: "aria-live"
          description: "Live region for dynamic content"
      priority: "high"
      evidence:
        - type: "automated"
          check: "aria-live-polite-or-assertive"
  
  semanticRole:
    role: "alert"
    component: "alert-banner"
    screenReaderAnnouncement: "Alert: Important message"
    properties: ["aria-live", "aria-atomic"]
```

---

## 8. Notes on Examples

### 8.1 Non-Normative

These examples are **non-normative** and provided for illustration only. Implementations MAY vary in structure while remaining conformant to the OAC specification.

### 8.2 Evidence Types

Examples demonstrate three evidence types:
- **Automated**: Machine-verifiable checks (contrast ratios, ARIA attributes)
- **Manual**: Human-verified checks (screen reader testing, keyboard navigation)
- **Documentation**: Documented decisions and exemptions

### 8.3 Real-World Usage

In practice:
- Evidence is typically collected by testing tools and stored separately
- Obligations are declared at design/development time
- Evidence accumulates over time through CI/CD pipelines and manual testing
- Semantic roles guide implementation but don't prescribe exact HTML/CSS

### 8.4 Tooling Integration

These examples are designed to work with:
- Accessibility testing tools (axe-core, Lighthouse, WAVE)
- Screen readers (NVDA, JAWS, VoiceOver)
- CI/CD validation pipelines
- Accessibility audit workflows

---

## 9. References

- [OAC Specification](SPECIFICATION.md)
- [OAC Schema](SCHEMA.md)
- [WCAG 2.1](https://www.w3.org/TR/WCAG21/)
- [ARIA 1.2](https://www.w3.org/TR/wai-aria-1.2/)
