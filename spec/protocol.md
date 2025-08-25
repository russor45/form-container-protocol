# Form Container Protocol (FCP)

- **Status:** Draft  
- **Version:** 0.2.0
- **Created by:** Richard Russoniello, 2025

---

## 1. Purpose

This protocol defines a standardized interface for form containers that unifies modal dialogs, sidebars, drawers, and inline forms under a single positioning-based API. The protocol specifies form lifecycle management, submission contracts, and positioning behavior to ensure consistency across implementations.

## 1.1. What This Is & What This Is Not

### ✅ What This Is

- **Form‑first architecture:** The form is the primary concern; containers are abstracted, interchangeable shells.
- **Unified form container interface:** Consolidates modal, sidebar, drawer, and inline patterns.
- **Positioning-based abstraction:** Container presentation is determined by spatial placement.
- **Form lifecycle contract:** Standardizes submission, state management, and closure behavior.
- **Framework-agnostic protocol:** Ensures consistent form behavior across implementations.
- **LLM-friendly contracts:** Exposes a consistent contract for automated tools, LLMs, and AI-driven agents

### 🚫 What This Is Not

- **Visual design specification:** Does not define styling or theming.
- **Complete form solution:** Not focused on fields, validation, or data management.
- **Animation specification:** Does not specify transition timing or motion design.
- **Rigid positioning specification:** Does not require pixel-perfect positioning.
- **Single-framework solution:** Not tied to any specific UI framework.
- **Container‑first bias:** Does not prioritize container type over form behavior.

### Framework-Agnostic Contract, Framework-Specific Implementation

This protocol defines behavioral contracts that work across frameworks:

- **Contract**: `onSubmit` callback, `isDirty` state exposure, position-based presentation hints
- **Implementation**: Framework-specific components that honor the contract (React components, Vue composables, Svelte stores, etc.)

### Implementation Flexibility Within Behavioral Boundaries

Implementations have freedom to:
- ✅ Choose visual design, animations, styling
- ✅ Adapt positions for responsive breakpoints  
- ✅ Map positions to their design system's components
- ✅ Add framework-specific developer experience features

Implementations MUST maintain:
- ❌ Consistent form lifecycle (open → dirty → submit → close)
- ❌ Reliable state exposure (`isDirty`, `isSubmitting`)
- ❌ Position-to-pattern mapping expectations
- ❌ Submission contract compatibility

---

## 2. Scope

- **In Scope:** Form container positioning, form lifecycle management, submission contracts, state exposure, and responsive behavior.
- **Out of Scope:** Visual styling, form validation, field components, backend integration, animation specifications.

---

## 3. Position-Based Containers

Form containers MUST expose a single `position` property that determines both spatial placement and container behavior. Each position MUST map to an implementation specific container pattern.

The `position` property MUST accept only the following six values: 

- **center** — Container positioned centrally, is typically implemented as a modal dialog
- **left** — Container anchored to left edge, is typically implemented as a left sidebar
- **right** — Container anchored to right edge, is typically implemented as a right sidebar
- **top** — Container anchored to top edge, is typically implemented as a top drawer
- **bottom** — Container anchored to bottom edge, is typically implemented as a bottom drawer
- **inline** — Container embedded within content flow, is typically implemented as an inline form

### Position-to-Pattern Mapping

 Implementations MUST support all six positions and map each to its corresponding container pattern as described. Custom or additional position values are NOT permitted. Any value outside this set MUST be rejected or ignored by implementations.

| Position | Common Implementation | Typical Use Case                        |
| -------- | --------------------- | --------------------------------------- |
| `center` | Modal Dialog          | Settings, confirmations, creation forms |
| `left`   | Left Sidebar          | Navigation panels, filter forms         |
| `right`  | Right Sidebar         | Details panels, edit forms              |
| `top`    | Top Drawer            | Notification forms, quick actions       |
| `bottom` | Bottom Drawer         | Mobile forms, filter panels             |
| `inline` | Inline Form           | Edit-in-place, embedded forms           |

> **Note:** The "Common" and "Typical Use Case" column provides illustrative examples and is not prescriptive. Implementations may support additional use cases as appropriate.

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

  // Responsive positioning (optional)
  mobilePosition?: 'top' | 'bottom' | 'left' | 'right' | 'center' | 'inline' // default: undefined

  // Form behavior (optional)
  closeOnSuccess?: boolean // default: true
  preventCloseWhenDirty?: boolean // default: false

  // Event handlers (optional)
  onDirtyChange?: (isDirty: boolean) => void // default: undefined
  onCloseStart?: () => Promise<boolean> | boolean // default: undefined
  onCloseComplete?: () => void // default: undefined

  // Performance hints (optional)
  lazy?: boolean // default: false
  debounceMs?: number // default: 300
}
```

### Default Values

| Property                | Default Value | Description                                                     |
| ----------------------- | ------------- | --------------------------------------------------------------- |
| `closeOnSuccess`        | `true`        | Automatically closes container after successful form submission |
| `preventCloseWhenDirty` | `false`       | Allows closing container with unsaved changes                   |
| `lazy`                  | `false`       | Loads content immediately when component mounts                 |
| `debounceMs`            | `300`         | Debounce duration for dirty state change notifications         |

---

## 5. Responsive Positioning

The `mobilePosition` property MUST allow configuration of the container’s position for mobile viewports. Implementations MUST transition at the mobile breakpoint of exactly `768px`.

### Default Mobile Mappings

When `mobilePosition` is not specified, implementations SHOULD use these sensible defaults:

| Desktop Position | Default Mobile Position | Rationale                           |
| ---------------- | ----------------------- | ----------------------------------- |
| `left`           | `bottom`                | Navigation drawer → bottom sheet    |
| `right`          | `bottom`                | Details panel → bottom drawer       |
| `center`         | `center`                | Modal dialog remains modal          |
| `top`            | `bottom`                | Top drawer → bottom drawer          |
| `bottom`         | `bottom`                | Bottom drawer remains bottom        |
| `inline`         | `inline`                | Inline form remains inline          |

### Position Validation

Implementations SHOULD validate responsive position transitions to prevent inappropriate combinations:

| From Position | Valid Target Positions         |
| ------------- | ------------------------------- |
| `center`      | `center`, `bottom`              |
| `left`        | `left`, `bottom`, `center`      |
| `right`       | `right`, `bottom`, `center`     |
| `top`         | `top`, `bottom`, `center`       |
| `bottom`      | `bottom`, `center`              |
| `inline`      | `inline`                        |

---

## 6. Submission Contract

### SubmitResult

```typescript
type SubmitResult = 
  | { status: 'success'; data?: any }
  | { status: 'error'; errors: ErrorDetails; retry?: () => void }
  | { status: 'blocked'; reason: string; allowOverride?: boolean }
  | { status: 'pending'; progress?: number }
```

- **`success`**: Indicates successful submission completion
- **`error`**: Indicates submission failure with field-level and global error details
- **`blocked`**: Indicates intentional prevention due to business logic constraints
- **`pending`**: Indicates that the submission is in progress

Container implementations SHOULD interpret these results to determine closure behavior, error display, and state management.

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


// Pending submission (e.g., awaiting server response or async validation)
{ status: 'pending', message: 'Submitting form, please wait…' }
```

### Exception Handling

Unhandled exceptions thrown by the `onSubmit` callback SHOULD be handled by implementation-specific behavior. Consumers SHOULD catch exceptions and return appropriate `SubmitResult` values to maintain consistent container behavior.

---

## 7. State Exposure

Implementations MUST expose these states through their standard state management patterns:

| State          | Type      | Description                                   |
| -------------- | --------- | --------------------------------------------- |
| `isSubmitting` | `boolean` | True while `onSubmit` callback is pending     |
| `isDirty`      | `boolean` | True if form values differ from initial state |

---

## 8. Container Lifecycle

1. **Initialize** — Container prepares with `isDirty=false` and `isSubmitting=false`
2. **Open** — Applies positioning and initializes form state
3. **Interact** — Tracks state changes and manages dirty state notifications (debounced by `debounceMs`)
4. **Close Attempt** — Invokes `onCloseStart` callback; if it returns `false` or rejects, prevents closure
5. **Submit** — Sets `isSubmitting=true` and invokes `onSubmit` callback
6. **Process Result**:
   - `success`: Closes container when `closeOnSuccess=true`
   - `blocked`: Remains open, may trigger application notification systems
   - `error`: Displays validation feedback within container
7. **Close Complete** — Invokes `onCloseComplete` callback after container is fully closed

---

## 9. Performance Behavior

### Lazy Loading

When `lazy=true`, implementations SHOULD defer content loading until the container opens. This improves initial render performance for forms with expensive content.

### Debounced Notifications

The `debounceMs` property controls the debounce duration for `onDirtyChange` notifications. This prevents excessive callbacks during rapid user input.

---

## 10. Implementation Requirements

### Positioning Behavior

Implementations MUST map each position to appropriate container behavior:

- **`center`**: Modal-style behavior with focus management and backdrop interaction
- **`left|right`**: Sidebar-style behavior with side-anchored positioning
- **`top|bottom`**: Drawer-style behavior with edge-anchored positioning
- **`inline`**: Embedded behavior within document flow

### Accessibility

- Positioned containers (`center`, `left`, `right`, `top`, `bottom`) SHOULD implement appropriate ARIA dialog patterns
- Inline containers SHOULD follow standard form accessibility practices
- Focus management SHOULD be consistent with container positioning behavior

### Form Integration

- All containers MUST render a `<form>` element or equivalent semantic structure
- State management (`isDirty`, `isSubmitting`) MUST be exposed consistently
- Lifecycle events MUST fire reliably across all position types

## 10.1. Implementation Guidelines

### Position Interpretation Rules

Implementations MUST map positions to appropriate patterns but MAY adapt the specific presentation:

| Position | Required Pattern | Implementation Freedom |
|----------|-----------------|----------------------|
| `center` | Modal-style focus management, backdrop behavior | Visual design, animation style, exact centering method |
| `left/right` | Sidebar-style anchoring, non-blocking interaction | Overlay vs push-content, slide direction, width |
| `top/bottom` | Drawer-style edge anchoring, dismissible behavior | Slide animation, height, backdrop treatment |
| `inline` | Embedded in content flow, no focus trap | Visual boundaries, responsive behavior |

### Responsive Adaptation Requirements

When `mobilePosition` is specified, implementations MUST:
- Transition at `768px` breakpoint
- Maintain form state during position changes
- Preserve form behavior across position transitions
- Fire appropriate lifecycle events during transitions

### Framework Integration Patterns

Implementations SHOULD follow their framework's conventions while maintaining contract compatibility:

- **React**: Hook-based state management, ref patterns
- **Vue**: Composable-based state, reactive properties  
- **Svelte**: Store-based state, reactive declarations
- **Angular**: Service-based state, reactive forms integration

---

## 11. Slots

| Slot            | Required: Yes/No | Description                              |
| --------------- | -------- | ---------------------------------------- |
| `header`        | ❌ No       | Container title and optional description |
| `body`          | ✅ Yes      | Form fields and interactive content      |
| `footer`        | ❌ No       | Primary and secondary action elements    |
| `notifications` | ❌ No       | Reserved area for inline status messages |

---

## 12. Versioning

- **Specification updates** follow semantic versioning principles
- **Breaking changes** require major version increment with migration documentation
- **Implementation compatibility** maintained within major version boundaries
- **Deprecation notices** provided minimum one minor version before removal

---

### Appendices

- [A: Position Reference](appendices/A-position-reference.md)
- [B: Implementation Examples](appendices/B-implementation-examples.md)
- [C: Migration Guide](appendices/C-migration-guide.md)
- [D: Common Patterns](appendices/D-common-patterns.md)
- [E: Testing Guidelines](appendices/E-testing-guidelines.md)
- [F: Mental Model](appendices/F-mental-model.md)
- [G: Slot Contracts](appendices/G-slot-contracts.md)
- [H: ARIA Mapping](appendices/H-aria-mapping.md)