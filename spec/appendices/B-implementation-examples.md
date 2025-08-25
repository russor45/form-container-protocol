## Appendix B: Implementation Examples

### ShadCN Implementation

```typescript
import { Dialog, DialogContent } from '@/components/ui/dialog'
import { Sheet, SheetContent } from '@/components/ui/sheet'
import { Drawer, DrawerContent } from '@/components/ui/drawer'

function FormContainer({ position, children, ...props }: FormContainerProps) {
  switch (position) {
    case 'center':
      return (
        <Dialog {...props}>
          <DialogContent>{children}</DialogContent>
        </Dialog>
      )

    case 'left':
    case 'right':
      return (
        <Sheet {...props}>
          <SheetContent side={position}>{children}</SheetContent>
        </Sheet>
      )

    case 'top':
      return (
        <Sheet {...props}>
          <SheetContent side="top">{children}</SheetContent>
        </Sheet>
      )

    case 'bottom':
      return (
        <Drawer {...props}>
          <DrawerContent>{children}</DrawerContent>
        </Drawer>
      )

    case 'inline':
      return <div className="inline-form-container">{children}</div>
  }
}
```

### Material UI Implementation

```typescript
import { Dialog, Drawer, Paper } from '@mui/material'

function FormContainer({ position, children, ...props }: FormContainerProps) {
  switch (position) {
    case 'center':
      return <Dialog {...props}>{children}</Dialog>

    case 'left':
    case 'right':
    case 'top':
    case 'bottom':
      return (
        <Drawer anchor={position} {...props}>
          {children}
        </Drawer>
      )

    case 'inline':
      return <Paper className="inline-form">{children}</Paper>
  }
}
```

---