## Appendix F: Mental Model - Form‑First, Presentation Agnostic

```
                    ┌─────────────────────────────┐
                    │      Form Container         │
                    │    (Behavioral Contract)    │
                    │-----------------------------│
                    │ • Form lifecycle mgmt       │
                    │ • Submission handling       │
                    │ • State exposure (dirty)    │
                    │ • Event coordination        │
                    │ • Position-based intent     │
                    └─────────────┬───────────────┘
                                  │
                                  ▼
            ┌─────────────────────────────────────────────────┐
            │         Position-Based Presentation             │
            │        (Implementation Determines UI)           │
            │-------------------------------------------------│
            │  center     left/right    top/bottom    inline  │
            │  ─────────  ──────────── ────────────── ─────── │
            │  • Modal    • Sidebar    • Drawer       • Embed │
            │  • Dialog   • Sheet      • Slide-up     • Flow  │
            │  • Overlay  • Side-out   • Push-down    • Page  │
            └─────────────────┬───────┬───────────────────────┘
                               │       │
                               ▼       ▼
                  ┌─────────────────┐   ┌─────────────────┐
                  │ Implementation  │   │ Implementation  │
                  │   Libraries     │   │   Adaptation    │
                  │-----------------│   │-----------------│
                  │ • ShadCN        │   │ • Responsive    │
                  │ • Material UI   │   │ • Platform      │
                  │ • Ant Design    │   │ • Theme         │
                  │ • Custom        │   │ • Accessibility │
                  └─────────────────┘   └─────────────────┘
```

### Core Principles

#### Form-First Design

The protocol prioritizes form behavior over presentation concerns. Position serves as a presentation hint, not a rigid specification.

#### Single Responsibility

Each position value represents a single spatial concept that maps to familiar UI patterns without constraining implementation details.

#### Implementation Freedom

UI libraries interpret positions using their own component systems, design languages, and interaction patterns.

#### Consistent Behavior

Form lifecycle, submission handling, and state management remain identical across all positions.

### Position Abstraction

```
Developer Intent → Position Value → Implementation Choice

"Modal form"     → center        → Dialog/Modal/Popup
"Side panel"     → right         → Sheet/Drawer/Sidebar
"Bottom sheet"   → bottom        → Drawer/Sheet/BottomSheet
"Inline edit"    → inline        → Form/Card/Section
```

### Framework Interpretation Examples

#### ShadCN Interpretation

- `center` → Dialog with DialogContent
- `right` → Sheet with SheetContent
- `bottom` → Drawer with DrawerContent
- `inline` → div with form styling

#### Material UI Interpretation

- `center` → Dialog with DialogContent
- `right` → Drawer with anchor="right"
- `bottom` → Drawer with anchor="bottom"
- `inline` → Paper with form styling

#### Ant Design Interpretation

- `center` → Modal
- `right` → Drawer with placement="right"
- `bottom` → Drawer with placement="bottom"
- `inline` → Form with layout="vertical"

### Behavioral Consistency

Regardless of implementation choice, all containers maintain:

```typescript
// Same form contract across all positions
interface FormBehavior {
  // State exposure
  isSubmitting: boolean
  isDirty: boolean

  // Lifecycle events
  onSubmit: SubmitHandler
  onDirtyChange: DirtyHandler
  onClose: CloseHandler

  // Behavior configuration
  closeOnSuccess: boolean
  resetOnClose: boolean
  preventCloseWhenDirty: boolean
}
```

### Responsive Adaptation

Position values can change based on context while maintaining form behavior:

```typescript
// Position adapts, form behavior stays consistent
const position = isMobile ? 'bottom' : 'right'
const position = isTablet ? 'center' : 'right'
const position = hasSpaceConstraints ? 'inline' : 'center'

// Same form contract regardless of position
<FormContainer position={position} onSubmit={handleSubmit} />
```

### Extension Pattern

New positions can be added without breaking existing contracts:

```typescript
// v1.0
position: 'center' | 'left' | 'right' | 'top' | 'bottom' | 'inline'

// v1.1 (non-breaking extension)
position: 'center' | 'left' | 'right' | 'top' | 'bottom' | 'inline' | 'floating'

// v1.2 (further extension)
position: '...' | 'corner' | 'split' | 'overlay'
```

Each new position inherits the same form contract while allowing implementation-specific interpretation.