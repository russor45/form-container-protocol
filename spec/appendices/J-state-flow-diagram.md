## Appendix J: Form Container State Flow

```text
 ┌───────────────────────────────────────────────────────────────┐
 │                      INITIALIZE (Closed)                      │
 │  open = false, isDirty = false, isSubmitting = false          │
 └───────────────┬───────────────────────────────────────────────┘
                 │ onOpenChange(true)
                 ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                          OPENED                               │
 │  open = true, isDirty = false, isSubmitting = false           │
 └───────────────┬───────────────────────────────────────────────┘
                 │ user edits form
                 ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                        DIRTY STATE                            │
 │  open = true, isDirty = true, isSubmitting = false            │
 │  → fires onDirtyChange(true)                                  │
 └───────────────┬───────────────────────────────────────────────┘
                 │ user submits
                 ▼
 ┌───────────────────────────────────────────────────────────────┐
 │                     SUBMITTING                                │
 │  open = true, isSubmitting = true                             │
 │  → disables inputs, shows loading state                       │
 └───────────────┬───────────────────────────────────────────────┘
                 │ onSubmit resolves with:
                 ├──────────────────────┬───────────────────────────────┐
                 ▼                      ▼                               ▼
        ┌──────────────────┐  ┌────────────────────────┐  ┌──────────────────────────┐
        │ SUCCESS          │  │ ERROR                  │  │ BLOCKED                  │
        │ status='success' │  │ status='error'         │  │ status='blocked'         │
        └──────────────────┘  └────────────────────────┘  └──────────────────────────┘
                 │                     │                               │
                 │ closeOnSuccess      │                               │ remain open
                 │ = true              │                               │ show reason
                 ▼                     ▼                               ▼
 ┌───────────────────────────────────────────────────────────────────────────────────────────────┐
 │  CLOSED (post-success)     │   OPENED (error state)            │   OPENED (blocked state)     │
 │  open=false                │   open=true                       │   open=true                  │
 │  resetOnClose? → reset     │   isDirty remains true            │   isDirty unchanged          │
 │  fires onClose()           │   show field/global errors        │   may trigger notifications  │
 └────────────────────────────┴───────────────────────────────────┴──────────────────────────────┘
```

### Key Transition Rules
- Dirty Tracking  
  - Any change from initial values sets isDirty=true and fires onDirtyChange(true).  
  - Resetting form or closing with resetOnClose=true sets isDirty=false.
- Prevent Close When Dirty  
  - If preventCloseWhenDirty=true, onOpenChange(false) is ignored until isDirty=false.
- Submission Lock  
  - While isSubmitting=true, ignore further submit attempts and disable close unless explicitly allowed.
- Error Handling  
  - SubmitResult.error populates notifications slot and/or inline field errors.
- Blocked Handling  
  - SubmitResult.blocked keeps container open, may trigger global alerts.