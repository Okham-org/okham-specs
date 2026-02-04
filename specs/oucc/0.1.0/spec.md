# OKHAM OUCC — UI Components Specification
- Contract: OUCC
- Version: 0.1.0
- Status: Draft
- Canonical Schema: https://okham.org/schemas/oucc/0.1.0/schema.json

This document specifies the OKHAM UI Components Standard for standardized user interface components across capabilities.

---

## 1. Overview

The UI Components Standard defines a common component library that ensures consistency, accessibility, and maintainability across all OKHAM capabilities.

### 1.1 Goals
- **Consistency**: Uniform appearance and behavior across capabilities
- **Accessibility**: WCAG 2.1 AA compliance built-in
- **Performance**: Optimized rendering and minimal bundle size
- **Developer Experience**: Familiar APIs and comprehensive documentation

### 1.2 Scope
This standard covers:
- Component APIs and props
- Styling and theming
- Accessibility requirements
- Implementation guidelines
- Testing requirements

---

## 2. Component Architecture

### 2.1 Component Categories

#### Layout Components
- **Container**: Responsive layout wrapper with max-width constraints
- **Grid**: CSS Grid-based 12-column layout system
- **Flex**: Flexbox utilities for flexible layouts
- **Card**: Content container with elevation and padding
- **Panel**: Collapsible content sections with headers

#### Form Components
- **Input**: Text input with validation states
- **Select**: Dropdown selection with search
- **Checkbox**: Boolean input with indeterminate state
- **Radio**: Single selection from multiple options
- **Button**: Action triggers with loading states
- **Form**: Form container with validation aggregation

#### Data Display Components
- **Table**: Sortable, filterable data tables
- **List**: Ordered/unordered content lists
- **Badge**: Status indicators with variants
- **Progress**: Linear and circular progress indicators
- **Chart**: Basic data visualization components

#### Navigation Components
- **Breadcrumb**: Hierarchical navigation path
- **Tabs**: Tabbed content organization
- **Menu**: Dropdown and context menus
- **Pagination**: Multi-page content navigation
- **Link**: Styled anchor elements

#### Feedback Components
- **Alert**: Status messages and notifications
- **Modal**: Overlay dialogs with backdrop
- **Toast**: Temporary notifications with auto-dismiss
- **Skeleton**: Loading state placeholders
- **Spinner**: Indeterminate loading indicators

### 2.2 Component API Structure

All components follow a consistent API pattern:

```typescript
interface ComponentProps {
  // Core props
  id?: string;
  className?: string;
  style?: React.CSSProperties;

  // State props
  disabled?: boolean;
  loading?: boolean;
  error?: string;

  // Event handlers
  onChange?: (value: any) => void;
  onFocus?: () => void;
  onBlur?: () => void;

  // Accessibility
  'aria-label'?: string;
  'aria-describedby'?: string;

  // Component-specific props
  [key: string]: any;
}
```

---

## 3. Component Declaration

Capabilities declare UI components in their `capability.yaml`:

```yaml
ui:
  components:
    - name: "user-management"
      type: "page"
      props:
        title: "User Management"
        layout: "table-view"
      children:
        - name: "user-table"
          type: "table"
          props:
            columns:
              - field: "name"
                label: "Name"
                sortable: true
              - field: "email"
                label: "Email"
                filterable: true
            dataSource: "/api/users"
            actions:
              - name: "edit"
                label: "Edit"
                type: "modal"
              - name: "delete"
                label: "Delete"
                type: "confirm"
```

### 3.1 Component Resolution

The UI rendering engine resolves component declarations:

1. **Type Resolution**: Maps component type to implementation
2. **Props Validation**: Validates props against component schema
3. **Dependency Injection**: Injects data sources and event handlers
4. **Rendering**: Generates HTML/CSS/JS output

### 3.2 Custom Components

Component declarations MUST include a top-level `description` that communicates semantic intent. Each `prop` and `slot` MUST also include a `description` field.

Capabilities can define custom components:

```yaml
ui:
  customComponents:
    - name: "user-profile-card"
      props:
        userId: { type: "string" }
        showActions: { type: "boolean", default: true }
      template: |
        <div class="user-profile">
          <img src="/api/users/{{userId}}/avatar" />
          <h3>{{user.name}}</h3>
          {{#if showActions}}
            <button onclick="editUser({{userId}})">Edit</button>
          {{/if}}
        </div>
```

---

## 4. Styling and Theming

### 4.1 Design Tokens

Components use standardized design tokens:

```css
:root {
  /* Colors */
  --color-primary: #3b82f6;
  --color-secondary: #64748b;
  --color-success: #10b981;
  --color-warning: #f59e0b;
  --color-error: #ef4444;

  /* Typography */
  --font-family: 'Inter', sans-serif;
  --font-size-sm: 0.875rem;
  --font-size-base: 1rem;
  --font-size-lg: 1.125rem;

  /* Spacing */
  --space-xs: 0.25rem;
  --space-sm: 0.5rem;
  --space-md: 1rem;
  --space-lg: 1.5rem;
  --space-xl: 2rem;

  /* Border radius */
  --radius-sm: 0.25rem;
  --radius-md: 0.375rem;
  --radius-lg: 0.5rem;

  /* Shadows */
  --shadow-sm: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
}
```

### 4.2 Theme Variants

Components support theme variants:

```yaml
ui:
  theme:
    variant: "dark"
    customColors:
      primary: "#8b5cf6"
      secondary: "#06b6d4"
```

### 4.3 Responsive Design

Components include responsive breakpoints:

```css
/* Mobile first */
.component {
  padding: var(--space-sm);
}

/* Tablet */
@media (min-width: 768px) {
  .component {
    padding: var(--space-md);
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .component {
    padding: var(--space-lg);
  }
}
```

---

## 5. Accessibility Requirements

### 5.1 WCAG 2.1 AA Compliance

All components must meet WCAG 2.1 AA requirements:

#### Color Contrast
- Normal text: 4.5:1 minimum contrast ratio
- Large text: 3:1 minimum contrast ratio
- Interactive elements: 3:1 minimum contrast ratio

#### Keyboard Navigation
```javascript
// Focus management
component.addEventListener('keydown', (e) => {
  if (e.key === 'Enter' || e.key === ' ') {
    e.preventDefault();
    component.click();
  }
  if (e.key === 'Escape') {
    component.blur();
  }
});
```

#### Screen Reader Support
```html
<button
  aria-label="Save changes"
  aria-describedby="save-description"
  role="button"
  tabindex="0"
>
  Save
</button>
<span id="save-description" class="sr-only">
  Saves the current form data
</span>
```

#### Focus Indicators
```css
*:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}
```

### 5.2 Motion Preferences

Respect user motion preferences:

```css
@media (prefers-reduced-motion: reduce) {
  .component {
    transition: none;
    animation: none;
  }
}
```

---

## 6. Implementation Guidelines

### 6.1 Component Registration

Capabilities register components in their `main.go`:

```go
func init() {
    // Register custom components
    ui.RegisterComponent("user-profile", &UserProfileComponent{})

    // Register data sources
    ui.RegisterDataSource("users", &UserDataSource{})
}
```

### 6.2 Component Lifecycle

Components follow a standard lifecycle:

```typescript
class MyComponent {
  constructor(props: ComponentProps) {
    this.props = props;
    this.state = {};
  }

  componentDidMount() {
    // Setup event listeners
    this.loadData();
  }

  componentDidUpdate(prevProps: ComponentProps) {
    // Handle prop changes
    if (prevProps.userId !== this.props.userId) {
      this.loadUserData();
    }
  }

  componentWillUnmount() {
    // Cleanup
    this.removeEventListeners();
  }

  render(): HTMLElement {
    return this.createElement();
  }
}
```

### 6.3 Testing Requirements

Components must include comprehensive tests:

```javascript
describe('Button Component', () => {
  it('renders correctly', () => {
    const button = render(<Button>Click me</Button>);
    expect(button).toBeInTheDocument();
  });

  it('handles click events', () => {
    const onClick = jest.fn();
    const button = render(<Button onClick={onClick}>Click me</Button>);
    fireEvent.click(button);
    expect(onClick).toHaveBeenCalled();
  });

  it('meets accessibility standards', () => {
    const button = render(<Button>Click me</Button>);
    expect(button).toHaveAttribute('role', 'button');
  });
});
```

### 6.5 Rendering Profiles and Artifacts

**Note:** This section supersedes previous engine-specific implementation details. OUCC is engine-neutral and defines how components reference rendering implementations.

Components MUST declare rendering implementations through **profiles**. Each profile specifies an engine and an **artifact reference**, not inline code.

#### 6.5.1 Profiles Declaration

Components declare zero or more rendering profiles:

```yaml
id: "okham_ui_card"
name: "Card"
version: "1.0.0"
category: "layout"
status: "stable"

profiles:
  - engine: templ
    artifact:
      kind: file
      path: templ/card.templ
      sha256: "a1b2c3d4e5f6789abcdef0123456789abcdef0123456789abcdef0123456789"

  - engine: thymeleaf
    artifact:
      kind: file
      path: thymeleaf/card.html
      sha256: "b2c3d4e5f6789abcdef0123456789abcdef0123456789abcdef0123456789a"
```

**Normative Rules:**

- **OUCC-PROF-001**: Components MAY declare zero or more profiles.
- **OUCC-PROF-002**: Each profile MUST be uniquely identified by `(component.id, component.version, engine)`.
- **OUCC-PROF-003**: The `engine` field MUST be a non-empty string identifying the rendering engine.
- **OUCC-PROF-004**: Profiles are additive; multiple engines may coexist for the same component.
- **OUCC-PROF-005**: OUCC does NOT standardize template languages or engine behavior; it only defines the reference structure.

#### 6.5.2 Artifact Structure

The `artifact` object describes how to locate and verify the rendering implementation:

```yaml
artifact:
  kind: file           # REQUIRED: artifact type (currently: "file")
  path: templ/card.templ  # REQUIRED for kind=file: relative path
  sha256: "a1b2c3..."  # REQUIRED for kind=file: SHA-256 hash
```

**Normative Rules:**

- **OUCC-ART-001**: The `artifact` field MUST be present in each profile.
- **OUCC-ART-002**: The `artifact.kind` field MUST be one of: `file` (other kinds reserved for future use: `uri`, `kv`).
- **OUCC-ART-003**: For `kind: file`, the `path` field MUST specify a relative path from the component package root.
- **OUCC-ART-004**: For `kind: file`, the `sha256` field MUST contain the SHA-256 hash (lowercase hex, 64 characters) of the raw file bytes.
- **OUCC-ART-005**: Artifact paths MUST NOT traverse outside the component package (no `../` escapes beyond root).
- **OUCC-ART-006**: Artifacts SHOULD be UTF-8 encoded text files where applicable.

#### 6.5.3 Artifact Resolution

Implementations resolve artifacts based on `kind`:

**For `kind: file`:**

1. Locate the component package root (e.g., `catalog/components/card/`)
2. Resolve `artifact.path` relative to that root
3. Read file bytes
4. Compute SHA-256 hash of raw bytes
5. Compare computed hash with `artifact.sha256`
6. If mismatch, reject artifact (integrity failure)
7. Parse artifact content according to engine requirements
8. Cache parsed artifact keyed by `(component.id, version, engine, sha256)`

**Reserved for future use:**
- `kind: uri`: Remote artifact via HTTP/HTTPS (requires `uri` and `sha256` fields)
- `kind: kv`: Artifact stored in NATS KV (requires `bucket`, `key`, and `sha256` fields)

**Normative Rules:**

- **OUCC-RES-001**: Implementations MUST verify `sha256` before using an artifact.
- **OUCC-RES-002**: Implementations MUST reject artifacts whose computed hash does not match the declared hash.
- **OUCC-RES-003**: Path resolution MUST be sandboxed to the component package root.
- **OUCC-RES-004**: Implementations MAY cache artifacts but MUST invalidate cache if `sha256` changes.

#### 6.5.4 Hash Computation

The `sha256` field represents the SHA-256 hash of the **raw file bytes** (not text content):

```bash
# Example: computing SHA-256 for a templ file
sha256sum catalog/components/card/templ/card.templ
# Output: a1b2c3d4e5f6789abcdef0123456789abcdef0123456789abcdef0123456789
```

**Normative Rules:**

- **OUCC-HASH-001**: Hashes MUST be computed from raw file bytes without encoding transformations.
- **OUCC-HASH-002**: Hashes MUST be represented as lowercase hexadecimal strings (64 characters).
- **OUCC-HASH-003**: Line ending normalization MUST NOT be applied before hashing (hash the file as-is).
- **OUCC-HASH-004**: Tooling MAY provide commands to compute and update hashes automatically.

#### 6.5.5 Engine-Specific Conventions (Non-Normative)

While OUCC does not standardize engines, common conventions include:

**templ engine:**
- Path pattern: `templ/{component-name}.templ`
- Content: Go templ template syntax
- Function name: PascalCase of component name (e.g., `Card` for `okham_ui_card`)

**thymeleaf engine:**
- Path pattern: `thymeleaf/{component-name}.html`
- Content: Thymeleaf HTML template syntax
- Namespace: `th:*` attributes for logic

**Other engines** (mustache, handlebars, etc.) MAY be added by implementations.

#### 6.5.6 Profile Versioning and Compatibility

Profiles are versioned alongside the component:

- **OUCC-VER-001**: A component version change (e.g., `1.0.0` → `1.1.0`) MAY include new profiles or updated artifacts.
- **OUCC-VER-002**: Artifact `sha256` changes constitute a content change and SHOULD trigger component version increment.
- **OUCC-VER-003**: Removing a profile for an engine SHOULD be treated as a breaking change if consumers depend on it.
- **OUCC-VER-004**: Adding new profiles is non-breaking (additive change).

#### 6.5.7 Deprecated: Inline Templates

Previous versions of OUCC included a `template` field with inline code:

```yaml
# DEPRECATED - DO NOT USE
template: |
  <div>Inline code here</div>
```

**This approach is explicitly discouraged:**

- Violates separation of concerns (data vs. code)
- Makes versioning and integrity checking difficult
- Couples component declarations to specific template languages
- Creates large, unmanageable YAML files

**Migration Path:**

If a component currently uses inline templates:

1. Extract template content to a separate file (e.g., `templ/component.templ`)
2. Compute SHA-256 hash of the file
3. Replace `template:` field with `profiles:` array
4. Reference the file via `artifact.kind=file`

#### 6.5.8 Example: Complete Component with Profiles

```yaml
component: "1.0.0"
id: "okham_ui_card"
name: "Card"
description: "Content container with optional title and elevation"
version: "1.0.0"
status: "stable"
category: "layout"

props:
  - name: "title"
    type: "string"
    required: false
  - name: "variant"
    type: "string"
    required: false
    default: "elevated"
    enum: ["elevated", "outlined", "flat"]

profiles:
  - engine: templ
    artifact:
      kind: file
      path: templ/card.templ
      sha256: "a1b2c3d4e5f6789abcdef0123456789abcdef0123456789abcdef0123456789"

  - engine: thymeleaf
    artifact:
      kind: file
      path: thymeleaf/card.html
      sha256: "b2c3d4e5f6789abcdef0123456789abcdef0123456789abcdef0123456789a"
```

---

## 7. Validation and Compliance

### 7.1 Component Validation

All component declarations are validated:

```yaml
# Valid component declaration
ui:
  components:
    - name: "user-table"
      type: "table"
      props:
        columns: []  # Required
        dataSource: "/api/users"  # Valid URL
```

### 7.2 Compliance Checking

Automated compliance verification:

```bash
# Validate component accessibility
okham ui validate-accessibility --component user-table

# Check performance metrics
okham ui benchmark --component user-table

# Verify theme compatibility
okham ui validate-theme --component user-table --theme dark
```

---

## 8. Versioning and Evolution

### 8.1 Component Versioning

Components follow semantic versioning:

```yaml
ui:
  components:
    - name: "data-table"
      version: "2.1.0"
      type: "table"
      breaking: false  # Backward compatible
```

### 8.2 Migration Guidelines

When updating components:

1. **Deprecation Notices**: Mark old props as deprecated
2. **Migration Helpers**: Provide codemods for prop changes
3. **Documentation Updates**: Update examples and guides
4. **Testing**: Ensure backward compatibility

---

## Implementation Notes (Non-Normative)

This section provides guidance for implementing OUCC-compliant UI component systems. These notes are **non-normative** and do not impose additional requirements beyond the specification.

### Typical Pitfalls

**Pitfall 1: Mixing Component Structure and Styling**

Embedding visual styles (colors, fonts, spacing) in component definitions couples them to specific design systems.

**Solution**: Use OUCC for component structure and semantics. Use ODT (Open Design Tokens) for visual styling. Reference tokens in component implementations, not in OUCC definitions.

**Pitfall 2: Framework-Specific Props**

Props that only work in specific frameworks (e.g., React's `className`, Vue's `v-model`) break portability.

**Solution**: Use framework-agnostic prop names. Let renderers map OUCC props to framework-specific props.

**Pitfall 3: Missing Accessibility Metadata**

Omitting accessibility metadata makes components unusable for assistive technologies.

**Solution**: Always include `semantics.accessibility` with at minimum `role` and `keyboard` support. Add ARIA attributes for complex components.

**Pitfall 4: Overly Generic Components**

Components like "Container" or "Wrapper" provide no semantic value.

**Solution**: Use domain-specific component names that describe their purpose (e.g., "ArticleCard", "UserProfile", "NavigationMenu").

### Minimal Viable Implementation

A minimal OUCC implementation requires:

1. **Component Registry**: Store component definitions as JSON/YAML files or in a database
2. **Prop Validation**: Validate props against component schema
3. **Slot Resolution**: Resolve slot content (children, named slots)
4. **Renderer**: Implement rendering for target framework (React, Vue, Svelte, etc.)
5. **Accessibility Support**: Ensure rendered components include accessibility attributes

### Example Component Rendering

**Scenario**: Render a Button component in React

**OUCC Definition**:
```json
{
  "id": "com.example.Button@1.0.0",
  "props": {
    "label": { "type": "string", "required": true },
    "variant": { "type": "string", "enum": ["primary", "secondary"] }
  },
  "slots": {
    "icon": { "optional": true }
  },
  "semantics": {
    "accessibility": {
      "role": "button",
      "keyboard": true
    }
  }
}
```

**React Renderer**:
```jsx
function renderButton(component, props, slots) {
  return (
    <button
      className={`btn btn-${props.variant || 'primary'}`}
      role="button"
      tabIndex={0}
    >
      {slots.icon && <span className="btn-icon">{slots.icon}</span>}
      {props.label}
    </button>
  );
}
```

### Component Composition

**Scenario**: Compose a Card component with Header, Content, and Footer slots

**OUCC Definition**:
```json
{
  "id": "com.example.Card@1.0.0",
  "slots": {
    "header": { "optional": true },
    "content": { "required": true },
    "footer": { "optional": true }
  }
}
```

**Usage**:
```yaml
component: com.example.Card@1.0.0
slots:
  header:
    - component: com.example.Heading@1.0.0
      props:
        text: "Card Title"
  content:
    - component: com.example.Text@1.0.0
      props:
        text: "Card content goes here"
  footer:
    - component: com.example.Button@1.0.0
      props:
        label: "Learn More"
```

### Performance Considerations

**Component Caching**: Cache compiled component definitions to avoid repeated parsing.

**Lazy Loading**: Load component definitions on-demand, not at startup.

**Virtual DOM**: Use virtual DOM diffing for efficient re-rendering (if applicable to framework).

**Memoization**: Memoize rendered components when props haven't changed.

**Code Splitting**: Split component bundles by route or feature for faster initial load.

### Accessibility Best Practices

**Keyboard Navigation**: Ensure all interactive components are keyboard-accessible (tab, enter, space, arrow keys).

**ARIA Attributes**: Use appropriate ARIA roles, states, and properties for complex components.

**Focus Management**: Manage focus for modals, dropdowns, and other overlay components.

**Screen Reader Support**: Test components with screen readers (NVDA, JAWS, VoiceOver).

**Color Contrast**: Ensure text and interactive elements meet WCAG contrast requirements (use ODT for colors).

### Testing Components

**Unit Tests**: Test component rendering with various prop combinations.

**Accessibility Tests**: Use automated tools (axe, pa11y) to check accessibility.

**Visual Regression Tests**: Capture screenshots and compare across changes.

**Example Test**:
```javascript
describe('Button Component', () => {
  it('should render with label', () => {
    const component = loadComponent('com.example.Button@1.0.0');
    const props = { label: 'Click Me' };
    const rendered = renderComponent(component, props);
    expect(rendered).toContain('Click Me');
  });

  it('should have button role', () => {
    const component = loadComponent('com.example.Button@1.0.0');
    const props = { label: 'Click Me' };
    const rendered = renderComponent(component, props);
    expect(rendered).toHaveAttribute('role', 'button');
  });

  it('should be keyboard accessible', () => {
    const component = loadComponent('com.example.Button@1.0.0');
    const props = { label: 'Click Me' };
    const rendered = renderComponent(component, props);
    expect(rendered).toHaveAttribute('tabIndex', '0');
  });
});
```

---

## Related Documents

- [UI Components Overview](../README.md)
- [UI Components Examples](../EXAMPLES.md)
- [UI Components Schema](../SCHEMA.md)
- [UI Components Governance](../GOVERNANCE.md)
- [UI-Kit Standard](../../ui-kit/README.md)
- [Accessibility Guidelines](../../accessibility/README.md)
