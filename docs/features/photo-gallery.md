# Photo Gallery

A **Photo Gallery** display tab manages multiple photos on an item, folder or filter. It is separate from a single [Thumbnail property](../entities/properties.md#thumbnail-properties).

## View photos

Open the item's configured photo tab. Switch between **Photo gallery** and the data-grid view, select a photo to open the viewer, and use **Load more photos** in the expanded gallery when available. Captions, download and delete actions depend on the tab configuration and your access.

The source matters:

| Source | Behavior |
|---|---|
| **Versioned attachments** | Photos attached to the host item's version. |
| **Folder children** | Photo entities placed structurally inside a folder. |
| **Filter results** | Read-only photo query results. |
| **Automatic** | Chooses attachments for ordinary entities, children for folders and query results for Filter Items. |

## Add photos

1. Open an editable current item and use the photo tab's add action.
2. Choose a **Photo category** if more than one is available.
3. Select JPEG, PNG or WebP files, up to 50 MB per photo.
4. Review the queue and click **Add to gallery**.
5. Save the containing item to commit the pending gallery changes.

**Add to gallery** stages the files; it does not save the parent item. Cancelling the containing editor discards pending changes. If saving fails, read the error and keep the editor open while correcting it.

Use a photo's Delete action, or select rows in the list and choose **Delete selected**, then confirm and save the containing item. A read-only or filter gallery does not offer these edits. Historical-version views are not an editable current gallery.

## Administrator configuration

Create or edit a **Photo Gallery** definition in **Configuration → Display Tabs** and assign it to the relevant categories. In its settings, choose the photo source, default upload category, visible categories, default layout, mosaic size, gallery page size and allowed actions. Photo upload categories must be occurrence categories with Photo behavior.

Category-tab permissions and normal record/file access still apply. A configured upload or delete switch does not override permissions. For filter galleries, **Include photos attached to entities in this scope** in the filter criteria controls whether scoped entities' media attachments are included.

## Related

- [Display Tabs](../admin/display-tabs.md)
- [Files & documents](../entities/files.md)
- [Filter Items & Snapshots](filter-items.md)
