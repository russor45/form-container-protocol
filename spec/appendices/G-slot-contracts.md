## Appendix G: Slot Contract Table

| Slot            | Required | ARIA Role / Landmark                                                               | Behavioral Guarantees                                                                                                                | Content Guidelines                                                     |
| --------------- | -------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------- |
| `header`        | ✅       | `role="heading"` (with `aria-level` appropriate to context)                        | Always rendered at the top of the container. Should receive focus on open if no other element is auto-focused.                       | Title text, optional description, and optional close button.           |
| `body`          | ✅       | `role="region"` or implicit sectioning element                                     | Scrollable if content exceeds container height. Must preserve form semantics (`<form>` element or equivalent).                       | Form fields, grouped logically with labels and instructions.           |
| `footer`        | ✅       | `role="contentinfo"` or `role="region"`                                            | Always rendered after `body`. Should contain at least one primary action (submit) and may contain secondary actions (cancel, reset). | Action buttons, links, or other form submission controls.              |
| `notifications` | ✅       | `role="status"` (for non-blocking updates) or `role="alert"` (for blocking errors) | Dynamically updated based on submission results. Must be programmatically focusable for screen readers when populated.               | Inline status messages, validation summaries, or global error notices. |

**Notes:**

- Slots must be exposed as named children or equivalent composition API hooks so implementers can inject content.
- Implementations should ensure slot order is preserved for accessibility and predictable tab order.