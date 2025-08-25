## Appendix C: Migration Guide

### From Existing Components

#### ShadCN Components

```typescript
// Before
<Dialog open={isOpen} onOpenChange={setIsOpen}>
  <form onSubmit={handleSubmit}>...</form>
</Dialog>

<Sheet open={isOpen} onOpenChange={setIsOpen} side="right">
  <form onSubmit={handleSubmit}>...</form>
</Sheet>

<Drawer open={isOpen} onOpenChange={setIsOpen}>
  <form onSubmit={handleSubmit}>...</form>
</Drawer>

// After
<FormContainer position="center" open={isOpen} onOpenChange={setIsOpen} onSubmit={handleSubmit}>
  ...
</FormContainer>

<FormContainer position="right" open={isOpen} onOpenChange={setIsOpen} onSubmit={handleSubmit}>
  ...
</FormContainer>

<FormContainer position="bottom" open={isOpen} onOpenChange={setIsOpen} onSubmit={handleSubmit}>
  ...
</FormContainer>
```

#### Material UI Components

```typescript
// Before
<Dialog open={isOpen} onClose={() => setIsOpen(false)}>
  <form onSubmit={handleSubmit}>...</form>
</Dialog>

<Drawer anchor="right" open={isOpen} onClose={() => setIsOpen(false)}>
  <form onSubmit={handleSubmit}>...</form>
</Drawer>

// After
<FormContainer position="center" open={isOpen} onOpenChange={setIsOpen} onSubmit={handleSubmit}>
  ...
</FormContainer>

<FormContainer position="right" open={isOpen} onOpenChange={setIsOpen} onSubmit={handleSubmit}>
  ...
</FormContainer>