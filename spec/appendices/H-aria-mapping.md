## Appendix H: Position-to-ARIA Pattern Mapping

| Position         | Common Pattern               | WAI-ARIA Authoring Practices Reference                                  | Key Accessibility Requirements                                                           |
| ---------------- | ---------------------------- | ----------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `center`         | Modal Dialog                 | [Dialog (Modal)](https://www.w3.org/WAI/ARIA/apg/patterns/dialogmodal/) | `role="dialog"`, `aria-modal="true"`, focus trap, ESC to close, restore focus on close.  |
| `left` / `right` | Sidebar / Dismissible Dialog | [Dialog (Non-Modal)](https://www.w3.org/WAI/ARIA/apg/patterns/dialog/)  | `role="dialog"`, labelled by header, focus trap if modal, dismissible via close control. |
| `top` / `bottom` | Drawer / Slide-out Panel     | [Dialog (Modal)](https://www.w3.org/WAI/ARIA/apg/patterns/dialogmodal/) | Same as modal dialog; ensure off-screen content is hidden from assistive tech when open. |
| `inline`         | Inline Form                  | [Form Landmark](https://www.w3.org/WAI/ARIA/apg/patterns/form/)         | `role="form"` with accessible name, no focus trap, integrated into page reading order.   |

**Implementation Guidance:**

- Always ensure the `header` slot content is referenced by `aria-labelledby` on the container.
- For `notifications`, use `aria-live="polite"` for non-critical updates and `aria-live="assertive"` for errors.
- Inline forms should avoid modal semantics entirely to preserve natural document flow.