## Appendix E: Testing Guidelines

### Unit Testing

```typescript
describe('FormContainer', () => {
  it('should render center position as modal', () => {
    render(
      <FormContainer
        position="center"
        open={true}
        onOpenChange={jest.fn()}
        onSubmit={jest.fn()}
      >
        <div>Form content</div>
      </FormContainer>
    )

    expect(screen.getByRole('dialog')).toBeInTheDocument()
  })

  it('should handle form submission', async () => {
    const handleSubmit = jest.fn().mockResolvedValue({ status: 'success' })

    render(
      <FormContainer
        position="center"
        open={true}
        onOpenChange={jest.fn()}
        onSubmit={handleSubmit}
      >
        <button type="submit">Submit</button>
      </FormContainer>
    )

    fireEvent.click(screen.getByRole('button', { name: 'Submit' }))

    await waitFor(() => {
      expect(handleSubmit).toHaveBeenCalled()
    })
  })

  it('should close on success when closeOnSuccess is true', async () => {
    const handleOpenChange = jest.fn()
    const handleSubmit = jest.fn().mockResolvedValue({ status: 'success' })

    render(
      <FormContainer
        position="center"
        open={true}
        onOpenChange={handleOpenChange}
        onSubmit={handleSubmit}
        closeOnSuccess={true}
      >
        <button type="submit">Submit</button>
      </FormContainer>
    )

    fireEvent.click(screen.getByRole('button', { name: 'Submit' }))

    await waitFor(() => {
      expect(handleOpenChange).toHaveBeenCalledWith(false)
    })
  })
})
```

### Integration Testing

```typescript
describe('FormContainer Integration', () => {
  it('should handle complete form lifecycle', async () => {
    const FormWithContainer = () => {
      const [isOpen, setIsOpen] = useState(false)
      const [isDirty, setIsDirty] = useState(false)

      const handleSubmit = async (data) => {
        await new Promise(resolve => setTimeout(resolve, 100))
        return { status: 'success', data }
      }

      return (
        <>
          <button onClick={() => setIsOpen(true)}>Open Form</button>
          <FormContainer
            position="center"
            open={isOpen}
            onOpenChange={setIsOpen}
            onSubmit={handleSubmit}
            onDirtyChange={setIsDirty}
          >
            <input name="test" />
            <button type="submit">Submit</button>
          </FormContainer>
          <div data-testid="dirty-state">{isDirty ? 'dirty' : 'clean'}</div>
        </>
      )
    }

    render(<FormWithContainer />)

    // Open form
    fireEvent.click(screen.getByText('Open Form'))
    expect(screen.getByRole('dialog')).toBeInTheDocument()

    // Make form dirty
    fireEvent.change(screen.getByRole('textbox'), { target: { value: 'test' } })
    expect(screen.getByTestId('dirty-state')).toHaveTextContent('dirty')

    // Submit form
    fireEvent.click(screen.getByText('Submit'))

    // Should close after successful submission
    await waitFor(() => {
      expect(screen.queryByRole('dialog')).not.toBeInTheDocument()
    })
  })
})
```

### Accessibility Testing

```typescript
describe('FormContainer Accessibility', () => {
  it('should have proper ARIA attributes for modal position', () => {
    render(
      <FormContainer
        position="center"
        open={true}
        onOpenChange={jest.fn()}
        onSubmit={jest.fn()}
      >
        <div>Form content</div>
      </FormContainer>
    )

    const dialog = screen.getByRole('dialog')
    expect(dialog).toHaveAttribute('aria-modal', 'true')
  })

  it('should manage focus properly', () => {
    render(
      <FormContainer
        position="center"
        open={true}
        onOpenChange={jest.fn()}
        onSubmit={jest.fn()}
      >
        <input name="first" />
        <input name="second" />
      </FormContainer>
    )

    // First focusable element should receive focus
    expect(screen.getByDisplayValue('')).toHaveFocus()
  })
})
```

---