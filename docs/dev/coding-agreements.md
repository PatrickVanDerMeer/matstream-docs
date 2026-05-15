# Coding Agreements

This page is the authoritative reference for MatStream coding conventions. It is intended for AI agents and developers generating or reviewing code in the MatStream codebase. Follow these agreements exactly — prefer matching existing MatStream patterns over generic best practices.

---

## 1. Layering and models

```
Client (Browser)
  ↕  DTOs  (MatStream.Web.Shared)
Controller  (MatStream.API)
  ↕  Domain/App Models  (MatStream.Domain / MatStream.Application)
Service  (MatStream.Application)
  ↕  Domain Models
Repository  (MatStream.Infrastructure)
  ↕  SQL / Dapper
Database
```

**Rules:**

- DTOs are **only** for API ↔ Client communication. They do not appear inside services, repositories, or domain logic.
- Internal code (services, repositories) uses **domain models** and **app models** only. Never accept or return `*Dto` types from a service or repository method.
- Controllers are the only place that maps domain/app models ↔ DTOs.
- The chain is always: Controller ↔ Service ↔ Repository. No layer is skipped.

---

## 2. DTO conventions

- DTOs are **classes** (not records).
- DTO class names end with the suffix `Dto` — e.g. `CategoryDto`, `EntityUpsertDto`.
- DTOs live in the `MatStream.Web.Shared` project.
- DTO field names use friendly, client-facing names (no DB-style prefixes like `EV_`, `PV_`, `E_`).

**Example:**
```csharp
// ✅ Correct DTO in MatStream.Web.Shared
public sealed class NumberingSchemeDto
{
    public int NumberingSchemeId { get; set; }
    public string Name { get; set; } = "";
    public string Template { get; set; } = "";
    public bool IsEnabled { get; set; }
}
```

---

## 3. Domain / app model conventions

- Internal model field names **match DB column names** where possible, including the table-specific prefix (e.g. `EV_Name`, `PV_Decimal`, `E_WorkspaceID`, `ID_Name`).
- Use friendly names **only** when the value is computed or derived — not a stored DB column.

**Example:**
```csharp
// ✅ Correct domain model field names
public class ImportDef
{
    public int ImportDefID { get; set; }
    public int ID_WorkspaceID { get; set; }
    public string ID_Name { get; set; } = "";
    public bool ID_IsEnabled { get; set; }
    // Computed/derived — friendly name acceptable:
    public string DisplayLabel => $"{ID_Name} ({ID_Source})";
}
```

---

## 4. Service and controller result pattern

- Workspace-aware application services return `ServiceResult` or `ServiceResult<T>` consistently.
- Controllers map `ServiceResult<T>` → `ApiEnvelope<T>` uniformly. Internal result types do not leak to the client.
- The mapping in controllers follows the established `MapToApiEnvelope` / `Unwrap` pattern already used throughout the codebase.

---

## 5. SQL / data access

- **Avoid SQL column aliases** unless strictly required (e.g. disambiguating a join column that appears in two tables with the same name).
- Use `SELECT *` on a specific table alias (e.g. `ev.*`) rather than listing columns individually where possible — this avoids missing-comma bugs when columns are added later.
- Use `public abstract class CrudRepository<TDomain, TKey> : RepositoryBase, ICrudRepository<TDomain, TKey>` as the base class for standard CRUD repositories.
- Use `ContextDb` or `CoreDb` (the MatStream DB accessor helpers) instead of working directly with `IDbConnection`.

**Example:**
```csharp
// ✅ Correct — no alias, using ContextDb
const string sql = @"
    SELECT ev.*
    FROM dbo.tbl_EntityVer ev
    WHERE ev.EV_WorkspaceID = @WorkspaceID
      AND ev.EntityVerID    = @EntityVerID";

var row = await _contextDb.QuerySingleOrDefaultAsync<EntityVerRow>(scope, sql, new
{
    WorkspaceID  = workspaceId,
    EntityVerID  = entityVerId
}, ct);
```

---

## 6. C# style

- Keep method signatures on **one line** when possible. Split parameters across lines only when the line would be unreasonably long (>120 chars) or when it materially improves readability.
- Use `sealed` on service and repository implementations unless inheritance is explicitly needed.
- Prefer `var` for local variables when the type is obvious from the right-hand side.

---

## 7. Audit fields

- Audit stamping (`Created*`, `Modified*`) is handled in the repository/DB layer via `CrudRepository` helpers or `ContextDb` stamp methods.
- Use `StampAuditIfPossible` on create (sets both Created* and Modified*).
- Use `EnsureAuditFieldsOnUpdate` on update:
  - `Modified*` is always overwritten.
  - `Created*` is backfilled only if null (never overwritten on update).
- Services do **not** stamp audit fields. The repository layer owns this responsibility.

---

## 8. Blazor component conventions

### Async event handlers

Use `async Task` for event handler methods, not `async void`. Wire event callbacks with the discard pattern to avoid exceptions being swallowed:

```razor
// ✅ Correct
RegisterCommandHandler?.Invoke(cmd => _ = HandleCommandAsync(cmd));
private async Task HandleCommandAsync(string cmd) { ... }

// ❌ Wrong — async void swallows exceptions
private async void HandleCommandAsync(string cmd) { ... }
```

### Double-click handlers

Read row data from event args directly. Do not rely on a cached `_selected` field, which may not be updated before the double-click fires:

```csharp
// ✅ Correct
private async Task OnRowDoubleClick(RecordDoubleClickEventArgs<NumberingSchemeDto> args)
{
    if (_dialog is null || args.RowData is null) return;
    await _dialog.OpenEditAsync(args.RowData);
}
```

### Grid DataSource stability

Bind Syncfusion grids to a cached field, not a computed property. If `DataSource` returns a new list reference on every render, the grid resets its selection state:

```csharp
// ✅ Correct — cached field
private List<MyDto> _items = new();
// bind: DataSource="@_items"

// ❌ Wrong — new list on every render resets selection
private List<MyDto> Items => _allItems.Where(...).ToList();
```

### YesNoCancelDialog — PreventAutoClose

`YesNoCancelDialog` closes automatically after `OnYes` fires by default. If the Yes action can fail and the dialog should stay open to show an error, use `PreventAutoClose="true"` and close the dialog manually on success:

```razor
<YesNoCancelDialog PreventAutoClose="true" @bind-Visible="_visible" OnYes="ConfirmAsync">
    ...
</YesNoCancelDialog>
```

```csharp
private async Task ConfirmAsync()
{
    try
    {
        await SomeService.DeleteAsync(_selected.Id);
        _visible = false;         // close only on success
        await RefreshAsync();
    }
    catch (Exception ex)
    {
        _error = ex.Message;      // show error, dialog stays open
    }
}
```

---

## 9. When in doubt

- Prefer **minimal, incremental changes** that compile over large refactors.
- Match existing MatStream patterns over generic C# / Blazor best practices.
- Search the project knowledge base before introducing a new pattern.