# Form Container Protocol (FCP)

- **Status:** Draft  
- **Version:** 0.1.0
- **Created by:** Richard Russoniello, 2025

---

## 1. Purpose

This protocol defines a standardized interface for form containers that unifies modal dialogs, sidebars, drawers, and inline forms under a single positioning-based API. The protocol specifies form lifecycle management, submission contracts, and positioning behavior to ensure consistency across implementations.

## 1.1. What This Is & What This Is Not

### What This Is

- A **unified form container interface** that consolidates modal, sidebar, drawer, and inline form patterns
- A **positioning-based abstraction** where container presentation is determined by spatial placement
- A **form lifecycle contract** that standardizes submission, state management, and closure behavior
- A **framework-agnostic protocol** ensuring consistent form behavior across implementations

### What This Is Not

- Visual design specifications, styling systems, or theming frameworks
- Form field components, validation libraries, or input management systems
- UI component libraries or complete implementation solutions
- Backend integration patterns or API communication protocols
- Animation specifications, transition timing, or motion design

---

## 2. Scope

- **In Scope:** Form container positioning, form lifecycle management, submission contracts, and state exposure.
- **Out of Scope:** Visual styling, form validation, field components, backend integration, animation specifications.

---

## 3. Position-Based Containers

Form containers are positioned using a single `position` property that determines both spatial placement and container behavior. Each position maps to familiar UI patterns while maintaining consistent form lifecycle management.

### Position Values

- **`center`** — Container positioned centrally, typically as a modal dialog
- **`left`** — Container anchored to left edge, typically as a left sidebar
- **`right`** — Container anchored to right edge, typically as a right sidebar
- **`top`** — Container anchored to top edge, typically as a top drawer
- **`bottom`** — Container anchored to bottom edge, typically as a bottom drawer
- **`inline`** — Container embedded within content flow as an inline form

### Position-to-Pattern Mapping

| Position | Common Implementation | Typical Use Case                        |
| -------- | --------------------- | --------------------------------------- |
| `center` | Modal Dialog          | Settings, confirmations, creation forms |
| `left`   | Left Sidebar          | Navigation panels, filter forms         |
| `right`  | Right Sidebar         | Details panels, edit forms              |
| `top`    | Top Drawer            | Notification forms, quick actions       |
| `bottom` | Bottom Drawer         | Mobile forms, filter panels             |
| `inline` | Inline Form           | Edit-in-place, embedded forms           |

---

## 4. API Contract

### Core Interface

```typescript
interface FormContainerProps {
  // Positioning (required)
  position: 'top' | 'bottom' | 'left' | 'right' | 'center' | 'inline'
  open: boolean
  onOpenChange: (open: boolean) => void
  onSubmit: (
    data: Record<string, any>,
  ) => void | SubmitResult | Promise<SubmitResult>

  // Form behavior (optional)
  closeOnSuccess?: boolean // default: true
  resetOnClose?: boolean // default: false
  preventCloseWhenDirty?: boolean // default: false

  // Event handlers (optional)
  onDirtyChange?: (isDirty: boolean) => void
  onClose?: () => void
}
```

### Default Values

| Property                | Default Value | Description                                                     |
| ----------------------- | ------------- | --------------------------------------------------------------- |
| `closeOnSuccess`        | `true`        | Automatically closes container after successful form submission |
| `resetOnClose`          | `false`       | Preserves form data when container is closed                    |
| `preventCloseWhenDirty` | `false`       | Allows closing container with unsaved changes                   |

### Usage Examples

#### Settings Modal

```typescript
<FormContainer
  position="center"
  open={showSettings}
  onOpenChange={setShowSettings}
  onSubmit={handleSettingsSubmit}
>
  <!-- Form content -->
</FormContainer>
```

#### User Profile Sidebar

```typescript
<FormContainer
  position="right"
  open={showProfile}
  onOpenChange={setShowProfile}
  onSubmit={handleProfileSubmit}
  resetOnClose={true}
>
  <!-- Profile form -->
</FormContainer>
```

#### Mobile Filter Drawer

```typescript
<FormContainer
  position="bottom"
  open={showFilters}
  onOpenChange={setShowFilters}
  onSubmit={handleFilterSubmit}
  closeOnSuccess={false}
>
  <!-- Filter options -->
</FormContainer>
```

#### Inline Edit Form

```typescript
<FormContainer
  position="inline"
  open={isEditing}
  onOpenChange={setIsEditing}
  onSubmit={handleInlineSubmit}
  preventCloseWhenDirty={true}
>
  <!-- Inline edit fields -->
</FormContainer>
```

---

## 5. Submission Contract

### SubmitResult

```typescript
type SubmitResult =
  | { status: 'success'; data?: Record<string, unknown> }
  | {
      status: 'error'
      errors: { global?: string[]; fields?: Record<string, string> }
    }
  | { status: 'blocked'; reason: string }
```

- **`success`**: Indicates successful submission completion
- **`error`**: Indicates submission failure with field-level and global error details
- **`blocked`**: Indicates intentional prevention due to business logic constraints

Container implementations interpret these results to determine closure behavior, error display, and state management.

### Example Usage

```typescript
// Success with data
{ status: 'success', data: { id: 123, name: 'Updated User' } }

// Error with field-specific messages
{
  status: 'error',
  errors: {
    fields: { email: 'Invalid email format' },
    global: ['Network error occurred']
  }
}

// Blocked submission
{ status: 'blocked', reason: 'Insufficient permissions' }
```

---

## 6. State Exposure

Implementations must expose these states through their standard state management patterns:

| State          | Type      | Description                                   |
| -------------- | --------- | --------------------------------------------- |
| `isSubmitting` | `boolean` | True while `onSubmit` callback is pending     |
| `isDirty`      | `boolean` | True if form values differ from initial state |

---

## 7. Container Lifecycle

1. **Initialize** — Container prepares with `isDirty=false` and `isSubmitting=false`
2. **Open** — Applies positioning and initializes form state
3. **Interact** — Tracks state changes and manages dirty state notifications
4. **Submit** — Sets `isSubmitting=true` and invokes `onSubmit` callback
5. **Process Result**:
   - `success`: Closes container when `closeOnSuccess=true`, resets when `resetOnClose=true`
   - `blocked`: Remains open, may trigger application notification systems
   - `error`: Displays validation feedback within container
6. **Close** — Invokes `onClose` callback, respects `preventCloseWhenDirty` constraints

---

## 8. Responsive Behavior

Implementations may adapt container positioning based on viewport constraints. Responsive behavior is implementation-specific and should follow platform conventions.

### Common Responsive Patterns

- Desktop `right` sidebar → Mobile `bottom` drawer
- Desktop `center` modal → Mobile `bottom` sheet
- `inline` containers typically remain inline across breakpoints

### Responsive Implementation Example

```typescript
const position = useMediaQuery('(min-width: 768px)') ? 'right' : 'bottom'

<FormContainer
  position={position}
  open={isOpen}
  onSubmit={handleSubmit}
/>
```

---

## 9. Implementation Requirements

### Positioning Behavior

Implementations must map each position to appropriate container behavior:

- **`center`**: Modal-style behavior with focus management and backdrop interaction
- **`left|right`**: Sidebar-style behavior with side-anchored positioning
- **`top|bottom`**: Drawer-style behavior with edge-anchored positioning
- **`inline`**: Embedded behavior within document flow

### Accessibility

- Positioned containers (`center`, `left`, `right`, `top`, `bottom`) should implement appropriate ARIA dialog patterns
- Inline containers should follow standard form accessibility practices
- Focus management should be consistent with container positioning behavior

### Form Integration

- All containers must support standard form submission patterns
- State management (`isDirty`, `isSubmitting`) must be exposed consistently
- Lifecycle events must fire reliably across all position types

---

## 10. Slots

| Slot            | Required | Description                              |
| --------------- | -------- | ---------------------------------------- |
| `header`        | ✅       | Container title and optional description |
| `body`          | ✅       | Form fields and interactive content      |
| `footer`        | ✅       | Primary and secondary action elements    |
| `notifications` | ✅       | Reserved area for inline status messages |

---

## 11. Versioning

- **Specification updates** follow semantic versioning principles
- **Breaking changes** require major version increment with migration documentation
- **Implementation compatibility** maintained within major version boundaries
- **Deprecation notices** provided minimum one minor version before removal

---

### Appendices

- [A: Position Reference](spec/appendices/A-position-reference.md)
- [B: Implementation Examples](spec/appendices/B-implementation-examples.md)
- [C: Migration Guide](spec/appendices/C-migration-guide.md)
- [D: Common Patterns](spec/appendices/D-common-patterns.md)
- [E: Testing Guidelines](spec/appendices/E-testing-guidelines.md)
- [F: Mental Model](spec/appendices/F-mental-model.md)
- [G: Slot Contracts](spec/appendices/G-slot-contracts.md)
- [H: ARIA Mapping](spec/appendices/H-aria-mapping.md)