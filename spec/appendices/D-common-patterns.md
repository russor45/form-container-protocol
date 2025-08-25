## Appendix D: Common Patterns

### Settings Form

```typescript
<FormContainer
  position="center"
  open={showSettings}
  onOpenChange={setShowSettings}
  onSubmit={handleSettingsSubmit}
  closeOnSuccess={true}
>
  <FormHeader>Application Settings</FormHeader>
  <FormBody>
    <input name="theme" type="select" />
    <input name="language" type="select" />
    <input name="notifications" type="checkbox" />
  </FormBody>
  <FormFooter>
    <button type="submit">Save Settings</button>
    <button type="button" onClick={() => setShowSettings(false)}>
      Cancel
    </button>
  </FormFooter>
</FormContainer>
```

### User Profile Panel

```typescript
<FormContainer
  position="right"
  open={showProfile}
  onOpenChange={setShowProfile}
  onSubmit={handleProfileUpdate}
  resetOnClose={false}
  preventCloseWhenDirty={true}
>
  <FormHeader>Edit Profile</FormHeader>
  <FormBody>
    <input name="firstName" />
    <input name="lastName" />
    <input name="email" type="email" />
    <textarea name="bio" />
  </FormBody>
  <FormFooter>
    <button type="submit">Update Profile</button>
    <button type="button" onClick={() => setShowProfile(false)}>
      Cancel
    </button>
  </FormFooter>
</FormContainer>
```

### Filter Drawer

```typescript
<FormContainer
  position="bottom"
  open={showFilters}
  onOpenChange={setShowFilters}
  onSubmit={handleApplyFilters}
  closeOnSuccess={false}
>
  <FormHeader>Filter Results</FormHeader>
  <FormBody>
    <select name="category">
      <option value="">All Categories</option>
      <option value="electronics">Electronics</option>
      <option value="clothing">Clothing</option>
    </select>
    <input name="minPrice" type="number" placeholder="Min Price" />
    <input name="maxPrice" type="number" placeholder="Max Price" />
  </FormBody>
  <FormFooter>
    <button type="submit">Apply Filters</button>
    <button type="button" onClick={handleClearFilters}>
      Clear All
    </button>
  </FormFooter>
</FormContainer>
```

### Inline Edit Form

```typescript
<FormContainer
  position="inline"
  open={isEditing}
  onOpenChange={setIsEditing}
  onSubmit={handleSaveChanges}
  preventCloseWhenDirty={true}
>
  <FormBody>
    <input name="title" defaultValue={item.title} />
    <textarea name="description" defaultValue={item.description} />
  </FormBody>
  <FormFooter>
    <button type="submit">Save</button>
    <button type="button" onClick={() => setIsEditing(false)}>
      Cancel
    </button>
  </FormFooter>
</FormContainer>
```

### Quick Action Form

```typescript
<FormContainer
  position="top"
  open={showQuickAction}
  onOpenChange={setShowQuickAction}
  onSubmit={handleQuickSubmit}
  closeOnSuccess={true}
>
  <FormHeader>Quick Add</FormHeader>
  <FormBody>
    <input name="name" placeholder="Enter name" autoFocus />
  </FormBody>
  <FormFooter>
    <button type="submit">Add</button>
    <button type="button" onClick={() => setShowQuickAction(false)}>
      Cancel
    </button>
  </FormFooter>
</FormContainer>
```

---