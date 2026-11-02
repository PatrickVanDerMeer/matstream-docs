# Dashboard

The Dashboard is the workspace home page at `/w/{workspaceSlug}/`. Its tiles show favourites, tasks, watched items, workspace statistics and recently modified records. Available tiles depend on your access.

## Tiles

| Tile | What it shows |
|---|---|
| **Favourites** | Your saved shortcuts. |
| **Tasks** | Your outstanding task items. See [Tasks](tasks.md). |
| **Watching** | Up to ten active item-change subscriptions, their change types, expiry and report counts. **My Subscriptions** opens the full list. |
| **Workspace KPIs** | Workspace counts for entities, versions, files, storage, users and categories. The current tile does not offer configurable business metrics or thresholds. |
| **Activity** | Recently modified entity rows with the modifying person and time. This is not a complete event audit trail. |

## Customize your dashboard

1. Click **Customize**.
2. Select the tiles you want to show, then **Apply**.
3. Click **Arrange** to move and resize tiles.
4. Click **Done** when finished.

Your layout is saved for your workspace user. **Reset to default** returns to the workspace default, or the built-in layout if no workspace default exists.

An administrator with `admin.workspace.settings` can use **Customize → Save as workspace default** to provide a starting layout. Personal layouts take precedence; changing the default does not replace colleagues' customizations.

If a tile fails to load, use its **Retry** action. A failure in one tile does not mean the whole workspace failed to open.

## Related

- [Item-change subscriptions](subscriptions.md)
- [Tasks](tasks.md)
- [Library](library.md)
- [Explorer](explorer.md)
