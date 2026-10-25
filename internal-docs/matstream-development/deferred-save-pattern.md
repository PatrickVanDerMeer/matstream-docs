# Deferred Save Pattern — In-Memory ViewModel Architecture

## Overview

MatStream uses a **deferred save model** for complex editor dialogs.
All changes are held in memory until the user explicitly clicks **Save**
or **Save and Close**. Clicking **Cancel** discards everything. Nothing
is written to the database during editing.

This pattern was first implemented for the Category Editor and should
be applied consistently to all similar dialogs.

---

## Core concepts

### ViewModels live client-side only

ViewModels are C# classes that live in `MatStream.Web.Assembly`. They
are **not DTOs** — they are never serialized or sent to the API. They
exist only to hold in-memory editing state between the UI and the save
operation.

```
MatStream.Web.Assembly/Features/{Feature}/ViewModels/
    {Entity}ViewModel.cs       — wraps a DTO + dirty/added/deleted flags
    {Entity}PermRowVm.cs       — wraps one permission row
```

A typical ViewModel looks like this:

```csharp
public sealed class CategoryPropViewModel
{
    public CategoryPropDto Prop { get; init; } = new();  // the DTO
    public bool IsDirty { get; set; }                    // field edits
    public bool IsAdded { get; set; }                    // new, not yet POSTed
    public bool IsDeleted { get; set; }                  // flagged for deletion
    public bool PermsLoaded { get; set; }                // lazy load flag
    public List<PermRowVm> Perms { get; set; } = new();  // in-memory perms
    public bool PermsDirty => Perms.Any(p => p.IsDirty || p.IsDeleted);
}
```

### Flags and their meaning

| Flag | Set when | Cleared when |
|---|---|---|
| `IsDirty` | Any field on the row is edited | SaveAsync completes |
| `IsAdded` | Row added in memory, not yet POSTed | SaveAsync POSTs and gets real ID |
| `IsDeleted` | User removes a row | SaveAsync sends DELETE, then removes from list |
| `PermsLoaded` | Perms fetched from API for first time | Never reset (perms stay in memory) |

### Permission rows (PermRowVm)

```csharp
public sealed class PermRowVm
{
    public int? PermId { get; set; }          // null = new
    public int WorkspaceGroupId { get; set; }
    public string GroupName { get; set; } = string.Empty;
    public bool CanView { get; set; }
    public bool CanEdit { get; set; }
    public bool IsDeleted { get; set; }
    public bool IsDirty { get; set; }
}
```

---

## Component architecture

### Ownership

The **tab panel component** owns the VM list and all API calls:

```
Dialog
  └── Panel (hosts tab strip)
        ├── Tab: General       → direct field binding, no VMs needed
        ├── Tab: {List A}      → Tab Panel owns List<{A}ViewModel>
        │     ├── Grid (data-down, @ref)
        │     └── Perm grid (data-down via Rows param)
        └── Tab: {List B}      → Tab Panel owns List<{B}ViewModel>
              ├── Grid (data-down, @ref)
              └── Perm grid (data-down via Rows param)
```

### Data flow — downward only

Tab panels pass their VM lists **down** to grids as `[Parameter]`.
Grids are pure rendering components — they never own or fetch data.

```razor
<MyGrid ViewModels="@_viewModels"
        SelectedChanged="OnSelectedAsync"
        OnDirty="OnGridDirtyAsync" />
```

### Data flow — upward via callbacks

Grids fire callbacks for user actions:

| Callback | When fired | What the panel does |
|---|---|---|
| `SelectedChanged` | Row clicked | Sets `_selectedVm`, lazy-loads perms if needed |
| `OnDirty` | Field edited or row dropped | Raises `OnDirtyChanged` to dialog |
| `AddRequested` | + button clicked | Panel opens picker, adds VMs to list |
| `RemoveRequested` | × button clicked | Panel sets `IsDeleted` or removes from list |

### Perm grids — write-back via SaveAllAsync

Perm grids (`CategoryPropertyPermGrid`, `CategoryTabPermGrid`) wrap
`WorkspaceGroupPermGrid` and use its `SaveAllAsync` callback to write
rows back to the parent VM **immediately on every change** — still
without hitting the API:

```
User ticks checkbox
  → WorkspaceGroupPermGrid.OnToggleAsync
  → NotifyDirtyAsync(true)
  → OnPermDirtyChanged(true)
  → _permGrid.SaveAsync()           ← flushes internal _rows
  → SaveAllPermsAsync(rows, ct)     ← our callback
  → RowsChanged.InvokeAsync(updated) ← writes back to VM.Perms
  → OnPermRowsChangedAsync          ← panel updates _selectedVm.Perms
```

---

## Lifecycle — how loading works

### The render loop problem

Blazor's `OnAfterRenderAsync` is called after every render — including
renders triggered by child component state changes. A naive load in
`OnAfterRenderAsync` can cause infinite loops.

### The correct pattern

Use **two separate guards**:

```csharp
private int? _loadedCategoryId;    // set AFTER API call completes
private int? _loadTriggeredForId;  // set IMMEDIATELY in OnParametersSet
```

```csharp
protected override void OnParametersSet()
{
    if (_loadTriggeredForId == CategoryId) return; // already triggered

    _loadTriggeredForId = CategoryId;  // prevent re-entry on next render
    _loadedCategoryId   = null;        // will be set after API responds
    _viewModels         = new();       // reset to empty
    _loadSeq++;
}

protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (IsCancelled || _loading) return;
    if (_loadedCategoryId == CategoryId || CategoryId is null or <= 0 || _grid is null) return;

    _loading = true;
    try
    {
        var vms = await FetchAsync(...);
        _loadedCategoryId = CategoryId;  // now mark as loaded
        _viewModels = vms;
        StateHasChanged();               // direct call — we're on render thread
        await Task.Yield();              // let Syncfusion process new DataSource
        if (_grid.ItemsCount > 0)
            await _grid.SelectFirstAsync(ct);
    }
    finally { _loading = false; }
}
```

### Why `_loadTriggeredForId` is needed

Without it, every render between "parameters set" and "API response
received" would reset `_viewModels = new()`, passing a new empty list
reference to the child grid, which triggers its `OnParametersSet`,
which triggers a re-render of the parent — an infinite loop.

### Lazy perm loading

Perms are loaded on first selection, not on initial load:

```csharp
private async Task OnSelectedAsync(MyViewModel? vm)
{
    _selectedVm = vm;

    if (vm is not null && !vm.PermsLoaded)
    {
        if (vm.EntityId > 0)
            await LoadPermsForVmAsync(vm);
        else
            vm.PermsLoaded = true; // new unsaved row — no perms yet
    }

    // Only call StateHasChanged after perm load — avoids re-rendering
    // the left grid (which resets Syncfusion selection state)
    if (vm is not null && !vm.PermsLoaded) // was false before load
        await InvokeAsync(StateHasChanged);
    else
        StateHasChanged();
}
```

---

## Grid component rules

### DataSource must be a stable reference

Syncfusion loses selection state when `DataSource` changes reference.
Cache the visible list — only rebuild when the parent list reference
actually changes:

```csharp
private List<MyViewModel> _visibleViewModels = new();
private List<MyViewModel>? _lastViewModels;

protected override void OnParametersSet()
{
    if (!ReferenceEquals(_lastViewModels, ViewModels))
    {
        _lastViewModels = ViewModels;
        _visibleViewModels = ViewModels.Where(v => !v.IsDeleted).ToList();
    }
}
```

Bind to the cached list:

```razor
<SfGrid DataSource="@_visibleViewModels" ... />
```

### Force rebuild after remove

When `IsDeleted` is set on a VM inside the list (same list reference),
`ReferenceEquals` will still return true and `_visibleViewModels` won't
rebuild. Force a rebuild by nulling the cache:

```csharp
// In parent, after setting vm.IsDeleted = true:
_grid?.NotifyViewModelsChanged();

// In grid:
public void NotifyViewModelsChanged() => _lastViewModels = null;
```

### Never call StateHasChanged inside OnParametersSetAsync

Blazor schedules a re-render automatically after `OnParametersSetAsync`
completes. Calling it manually schedules a second render, which
triggers `OnParametersSetAsync` again — a loop:

```csharp
// WRONG:
protected override async Task OnParametersSetAsync()
{
    if (ReferenceEquals(_loadedRows, Rows)) return;
    _loadedRows = Rows;
    await InvokeAsync(StateHasChanged); // ← causes loop
    if (_permGrid is not null)
        await _permGrid.ReloadAsync();
}

// CORRECT:
protected override async Task OnParametersSetAsync()
{
    if (ReferenceEquals(_loadedRows, Rows)) return;
    _loadedRows = Rows;
    if (_permGrid is not null)
        await _permGrid.ReloadAsync();
}
```

### Use StateHasChanged() directly in OnAfterRenderAsync

Inside `OnAfterRenderAsync` you are already on the render thread.
Use `StateHasChanged()` directly — `InvokeAsync(StateHasChanged)` adds
an extra dispatch cycle that can cause an additional `OnAfterRenderAsync`
call:

```csharp
// WRONG:
await InvokeAsync(StateHasChanged);

// CORRECT:
StateHasChanged();
```

### SelectRowAsync fires OnRowSelected — don't double-fire

`_sfGrid.SelectRowAsync(0)` fires the Syncfusion `RowSelected` event,
which fires your `SelectedPropertyChanged` callback. Don't also call
the callback directly in `SelectFirstAsync` — that fires it twice:

```csharp
public async Task SelectFirstAsync(CancellationToken ct = default)
{
    var first = ViewModels.FirstOrDefault();
    SelectedVm = first;

    if (_sfGrid is not null && first is not null)
        await _sfGrid.SelectRowAsync(0); // fires RowSelected → callback
    else
        if (SelectedChanged.HasDelegate)
            await SelectedChanged.InvokeAsync(first); // only if no grid
}
```

---

## SaveAsync order

When implementing `SaveAsync` on a tab panel, follow this order to
ensure foreign keys exist before they are referenced:

1. **Structural changes first** — POST new assignments (get real IDs back)
2. **Field updates** — PATCH dirty rows (requires real IDs from step 1)
3. **Deletions** — DELETE removed assignments
4. **Permissions** — upsert/delete perm rows (requires real IDs from step 1)
5. **Cleanup** — `RemoveAll(v => v.IsDeleted)` from the VM list
6. **Signal dirty cleared** — `_ = OnDirtyChanged.InvokeAsync(false)`

Example:

```csharp
public async Task SaveAsync(CancellationToken ct = default)
{
    if (ReadOnly) return;

    // 1. POST new assignments — get real IDs back
    foreach (var vm in _viewModels.Where(v => v.IsAdded && !v.IsDeleted))
    {
        var newId = await Service.AssignAsync(parentId, vm.Entity.DisplayId, ct);
        vm.Entity.Id = newId;
        vm.IsAdded = false;
    }

    // 2. PATCH dirty field changes
    foreach (var vm in _viewModels.Where(v => v.IsDirty && !v.IsDeleted && !v.IsAdded && v.Entity.Id > 0))
    {
        await Service.UpdateAsync(vm.Entity.Id, vm.Entity, ct);
        vm.IsDirty = false;
    }

    // 3. DELETE removed assignments
    foreach (var vm in _viewModels.Where(v => v.IsDeleted && !v.IsAdded && v.Entity.Id > 0))
        await Service.RemoveAsync(parentId, vm.Entity.Id, ct);

    // 4. Save permissions for surviving rows
    foreach (var vm in _viewModels.Where(v => !v.IsDeleted && v.PermsLoaded && v.PermsDirty && v.Entity.Id > 0))
    {
        foreach (var perm in vm.Perms.Where(p => p.IsDeleted && p.PermId is > 0))
            await Service.DeletePermAsync(perm.PermId!.Value, ct);

        foreach (var perm in vm.Perms.Where(p => p.IsDirty && !p.IsDeleted))
        {
            var saved = await Service.UpsertPermAsync(vm.Entity.Id, perm, ct);
            if (saved is not null) { perm.PermId = saved.Id; perm.IsDirty = false; }
        }

        vm.Perms.RemoveAll(p => p.IsDeleted);
    }

    // 5. Remove deleted VMs from the in-memory list
    _viewModels.RemoveAll(v => v.IsDeleted);

    // 6. Signal dirty cleared
    _ = OnDirtyChanged.InvokeAsync(false);
}
```

---

## RevertAsync

Cancel discards all in-memory state and reloads:

```csharp
public async Task RevertAsync()
{
    _loadedId           = null;
    _loadTriggeredForId = null;
    _selectedVm         = null;
    _viewModels         = new();
    _loadSeq++;
    _ = OnDirtyChanged.InvokeAsync(false);
    await InvokeAsync(StateHasChanged);
}
```

The panel's `OnAfterRenderAsync` will then see `_loadedId != CategoryId`
and trigger a fresh load from the API.

---

## Checklist for new features

When implementing this pattern for a new feature:

- [ ] Create `{Entity}ViewModel.cs` with `IsDirty`, `IsAdded`, `IsDeleted`, `PermsLoaded`, `Perms`
- [ ] Create `PermRowVm.cs` (or reuse the shared one from Categories/ViewModels)
- [ ] Tab panel owns `List<{Entity}ViewModel> _viewModels`
- [ ] Tab panel has `_loadedId` + `_loadTriggeredForId` guards
- [ ] Grid is data-down: `[Parameter] List<{Entity}ViewModel> ViewModels`
- [ ] Grid caches `_visibleViewModels` with `ReferenceEquals` guard
- [ ] Grid has `NotifyViewModelsChanged()` for force-rebuild after delete
- [ ] Grid fires `OnDirty` callback on field edit and row drop
- [ ] Perm grid uses `SaveAllAsync` + `RowsChanged` to write back in memory
- [ ] `OnPermDirtyChanged` calls `await _permGrid.SaveAsync()` before raising event
- [ ] `SaveAsync` follows the structural → field → delete → perms order
- [ ] `RevertAsync` resets all flags and reloads
- [ ] Dialog has Cancel (calls `RevertAsync`), Save, Save and Close buttons
- [ ] Save buttons disabled when `!IsDirty`
- [ ] Save clears dirty state and calls `OnDirtyChanged.InvokeAsync(false)`