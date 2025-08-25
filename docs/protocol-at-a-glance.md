# Form Container Protocol — At a Glance

```text
 ┌───────────────────────────────────────────────────────────────┐
 │ FORM CONTAINER                                                │
 │ (Behavioral Contract Layer)                                   │
 │───────────────────────────────────────────────────────────────│
 │ • Unified API: position, open, onOpenChange, onSubmit │
 │ • Lifecycle: init → open → interact → submit → process → close│
 │ • State: isSubmitting, isDirty                                │
 │ • Config: closeOnSuccess, resetOnClose, preventCloseWhenDirty │
 │ • Events: onDirtyChange, onClose                              │
 └───────────────┬───────────────────────────────────────────────┘
                 │
                 ▼
 ┌───────────────────────────────────────────────────────────────┐
 │ POSITION-BASED PRESENTATION                                   │
 │───────────────────────────────────────────────────────────────│
 │ center → Modal Dialog (ARIA modal dialog)                     │
 │ left → Sidebar (ARIA dialog)                                  │
 │ right → Sidebar (ARIA dialog)                                 │
 │ top → Drawer (ARIA modal dialog)                              │
 │ bottom → Drawer (ARIA modal dialog)                           │
 │ inline → Inline Form (ARIA form landmark)                     │
 └───────────────┬───────────────────────────────────────────────┘
                 │
                 ▼
 ┌───────────────────────────────────────────────────────────────┐
 │ SLOT CONTRACTS                                                │
 │───────────────────────────────────────────────────────────────│
 │ header → role="heading", labelled container                   │
 │ body → role="region", contains form fields                    │
 │ footer → role="contentinfo", primary + secondary actions      │
 │ notifications → role="status"/"alert", live region for updates│
 └───────────────────────────────────────────────────────────────┘
                 │
                 ▼
 ┌───────────────────────────────────────────────────────────────┐
 │ ACCESSIBILITY MAPPING                                         │
 │───────────────────────────────────────────────────────────────│
 │ • Modal positions: aria-modal="true", focus trap, ESC close   │
 │ • Non-modal sidebars: labelled, dismissible                   │
 │ • Inline: natural reading order, no trap                      │
 │ • Notifications: aria-live="polite"/"assertive" as appropriate│
 └───────────────────────────────────────────────────────────────┘
                 │
                 ▼
 ┌───────────────────────────────────────────────────────────────┐
 │ RESPONSIVE ADAPTATION                                         │
 │───────────────────────────────────────────────────────────────│
 │ Position can change with viewport, form behavior stays same   │
 │ e.g. right → bottom on mobile                                 │
 └───────────────────────────────────────────────────────────────┘
 ```