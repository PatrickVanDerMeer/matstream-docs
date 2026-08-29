# Task — Import images embedded in an Excel sheet

**Status:** Not started. Specced 2026-08-26.
**Prerequisite:** the Library Items text import (Configuration 4) must be verified working end to end first. This builds directly on its validate/commit flow.

## Goal

When a Library Items profile imports a sheet, extract the pictures embedded in that sheet and attach each one to the library item its row created or updated.

Two parts, separable and useful independently:

- **Part 1 — attach as Documents.** The real file, versioned, in the file store, visible on the Attached File tab. Covered below.
- **Part 2 — per-entity thumbnail.** A small unversioned image in the database, rendered as a column in the Explorer grids. Covered in [Thumbnails](#thumbnails-showing-the-image-in-a-grid). Worth building even if the images never come from Excel — MatStreamConnect could populate it from CAD previews just as well.

## Why this is cheap

Both ends already exist — the work is the wiring, plus one architectural fix.

| Piece | Status |
|---|---|
| Reading pictures out of .xlsx | ClosedXML 0.105.0, already referenced. `worksheet.Pictures`, `IXLPicture.TopLeftCell`, `IXLPicture.ImageStream` and `XLPicturePlacement` all confirmed present in the shipped assembly — no hand-parsing of `xl/drawings/*.xml` needed. |
| Storing a file against an entity | `IDocumentService.UploadAndLinkAsync(targetEntityId, documentCategoryId, originalFileName, fileLastModifiedUTC, fileCreatedUTC, contentType, content, ct)` — creates the Document entity and links it in one call. |
| Somewhere to keep the source file | `ImportBatch.IB_FileStorageKey` — the column exists on the model but is **never populated**. See the blocker below. |

## The blocker: the sheet is gone by commit time

Validate and commit are two separate HTTP calls. `StageFromExcelAsync` persists only cell *text* into `tbl_ImportBatchRowCell` and discards the uploaded workbook, so at commit there are no image bytes left to attach.

Two ways out:

1. **Persist the uploaded .xlsx at staging** into `IB_FileStorageKey`, and re-open it at commit to pull the pictures. *Recommended* — the column exists for exactly this, and it also gives an audit trail of precisely which file was imported.
2. Extract the images into the file store during staging, and only link them at commit.

Option 1 keeps staging cheap and puts the cost on the commit, which is already the slow half.

## What the sample files actually look like

Measured from two representative customer workbooks held outside the repository — **Sample A**, an item list with one image per row, and **Sample B**, a larger price list with mixed media.

| | **Sample A** | **Sample B** |
|---|---|---|
| Picture anchors | 10 | 32 |
| Media files | 9 (`.jpg`) | 31 — 21 `.jpeg`, 7 `.png`, 3 `.emf` |
| Anchor type | all `twoCellAnchor` | all `twoCellAnchor` |
| Rows covered | 2–11, one each | 30 distinct rows |
| Rows with >1 picture | none | rows 86 and 87 |

Two things fall out of this that the implementation must handle:

- **Anchors outnumber media in both files** (10 vs 9, 32 vs 31). The same image can be referenced by more than one anchor, so do not assume a 1:1 picture-to-file mapping or key anything on the media filename.
- **`.emf` is present.** Windows metafiles, almost certainly logos or decoration rather than product photos, and not displayable in a browser.

## Implementation outline

1. **Persist the workbook at staging** — `ImportBatchService.StageFromExcelAsync` writes the uploaded stream to the file store and sets `IB_FileStorageKey`.
2. **Extract at commit** — in `LibraryImportService.CommitAsync`, open the stored workbook once and build a `row number → List<IXLPicture>` map from `worksheet.Pictures` using `TopLeftCell.Address.RowNumber`.
3. **Attach after upsert** — for each row whose upsert returned an entity id, call `UploadAndLinkAsync` with the picture stream and the profile's configured image category.
4. **Report it in the log** — add an `ImagesAttached` count to `ImportValidationRow` so the grid shows which row got which image, and so a bad anchor match is visible rather than silent.
5. **New profile settings** — image Document category, and the re-import policy (below). `ID_SettingsJson` already exists, so this needs no schema change.

Validate should also report the picture count per row, so the dry run tells the user what will be attached before anything is written.

## Decisions still open

These are product calls, not technical ones. None of them block starting; all of them need answering before it ships.

- **Multiple images on one row** (rows 86/87 in the prices file) — attach all, or first only?
- **`.emf` files** — skip, or convert? Suggest whitelisting `jpg`/`jpeg`/`png`/`gif` and logging the rest as skipped, so nothing disappears quietly.
- **Re-import** — replace the attached image, add a new version, or skip when the item already has one? This is the same versioning question the text import faced; whatever is chosen should match its answer.
- **Image category** — which Document category do the attachments land in? Needs a picker on the Library Items profile tab.
- **No matching row** — a picture anchored outside any data row (a header logo, say). Skip silently or report it?

## Risks and gotchas

- **Anchoring is a heuristic, not data.** A picture "in" row 5 is a floating object whose top-left corner happens to land in row 5. Both sample files are well-behaved, but a tall row, a resized image or a `twoCellAnchor` spanning several rows can resolve to a row the user did not intend. This is why step 4 (surfacing the match in the log) matters more than it looks.
- **`XLPicturePlacement`** — `TopLeftCell` is only meaningful for pictures that move with cells. A picture set to free-floating may not give a usable anchor; handle that case rather than assuming.
- **Size.** Sample B's original workbook is ~4 MB. Persisting every imported workbook has a storage cost, and the upload path currently allows up to 200 MB. Consider whether staged files should be purged after a successful commit.
- **Workspace purge.** Anything new stored per workspace must stay purge-reachable — see the workspace purge invariant.

## Thumbnails — showing the image in a grid

A small image stored **per entity, without version history**, rendered as a column in `SfGrid` / `SfTreeGrid`. It is a display cache, never the system of record: it can always be regenerated from the attached Document, and nothing should read it as data.

### Schema

```
tbl_EntityThumbnail
  ET_EntityID       int PK, FK -> tbl_Entity ON DELETE CASCADE
  ET_Bytes          varbinary(max)
  ET_ContentType    nvarchar(100)
  ET_Width          int
  ET_Height         int
  ET_ModifiedUTC    datetime2
  ET_ModifiedByWUID int
```

One row per entity, overwritten in place. It hangs off `tbl_Entity`, **not** `tbl_EntityVer`, so it survives versioning and is not frozen when a version is superseded.

**It must be a side table.** The repositories use `SELECT *` extensively — `GetLatestVersionInfoAsync` does `SELECT ev.*`, and `vw_EntityCurrent` is queried broadly. A `varbinary(max)` on `tbl_Entity` or `tbl_EntityVer` would drag image bytes into essentially every entity read in the product. A 1:1 side table makes that impossible rather than merely unlikely.

`ON DELETE CASCADE` from `tbl_Entity` also satisfies the workspace purge invariant without the table needing its own `WorkspaceID`.

### Serving: URL, not base64

The Explorer TreeGrid has **no virtualization and no paging** — it binds every row. Embedding data URIs would put the entire image set in the grid payload on every refresh: 500 rows at ~20 KB of base64 is ~13 MB per bind.

Serve `GET /api/wid/{wid}/entities/{id}/thumbnail` instead, with an ETag and `Cache-Control`. The row carries only a `HasThumbnail` bool, the browser fetches lazily and caches, and rows scrolled past cost nothing.

### The column

The pattern already exists. `EntityTreeGridWebApi.razor` renders `IconCss` as a templated column over `IDictionary<string, object?>` rows, and already reads `StoredFileId` off the row to draw the paperclip badge. A thumbnail column is the same shape:

```razor
<TreeGridColumn Field="Thumbnail" HeaderText="" Width="52"
                AllowSorting="false" AllowFiltering="false" AllowEditing="false">
    <Template Context="obj">
        @{ var row = (IDictionary<string, object?>)obj; /* HasThumbnail + EntityId */ }
        @if (hasThumb)
        {
            <img src="@ThumbUrl(entityId)" class="ms-grid-thumb" loading="lazy" />
        }
    </Template>
</TreeGridColumn>
```

Add a `Thumbnail` entry to `EntityDefinitionBaseColumns` so it appears in the column chooser, and exclude it from sort/filter the same way `IconCss` already is.

### Generating them

**SkiaSharp 3.119.1 is already referenced** (it came in with the ZXing barcode work), so resizing needs no new dependency and no `System.Drawing`.

### Storage cost

Measured from the 37 real pictures in the two sample workbooks, re-encoded with Lanczos at quality 80. Every option below stays under 8000 bytes so the blob is stored **in-row** — no LOB pages, no pointer indirection. Page math at 8192 B/page, 8060 usable, ~63 B/row for the remaining columns plus row overhead:

| option | avg image | row | rows/page | 1M rows | wasted |
|---|---|---|---|---|---|
| 96px WebP | 1,140 B | 1,203 B | 6 | **1.27 GiB** | 16% |
| 128px WebP | 1,569 B | 1,632 B | 4 | **1.91 GiB** | 23% |
| 128px JPEG | 2,428 B | 2,491 B | 3 | **2.54 GiB** | 11% |
| 192px WebP | 2,318 B | 2,381 B | 3 | **2.54 GiB** | 15% |
| 256px WebP | 2,596 B | 2,659 B | 3 | **2.54 GiB** | 5% |
| 256px JPEG | 4,047 B | 4,110 B | 1 | **7.63 GiB** | 51% |

**128px WebP is the recommended default: ~1.91 GiB (about 1,953 MB as SSMS will report it) per million entities.**

Watch the cliff at the bottom of the table. A 4,110-byte row is just over half a page, so only one fits and half of every page is wasted — 256px JPEG costs three times 128px JPEG for a marginally bigger picture. Row size relative to 8060 bytes matters more than image size, which is why 128px JPEG and 256px WebP cost exactly the same.

Backups grow by the same amount, and **compression buys nothing** — the data is already compressed, so page compression and backup compression are both near no-ops here.

### Gotchas

- **Never name the row property `ImageUrl`.** `CategoryVisibilityTree.razor` already carries a comment warning that a property named `IconCss` / `Tooltip` / `ImageUrl` / `Selected` auto-binds in SfTreeView and duplicates the template's own icon. Use `ThumbnailUrl`, or keep it as `HasThumbnail`.
- **Cap the encoder output.** The largest 256px WebP in the sample came out at 12 KB against a 2.6 KB average. Re-encode at lower quality if a thumbnail exceeds ~8 KB, or one bad image pushes its row off-page and into LOB storage.
- The sample is 37 product photos. Screenshots and line drawings compress better, busy photographs worse — treat ±30% as the honest band.

### Open decisions

- Which grids get the column by default — Explorer only, or the Filter Result and Where Used grids too?
- Regeneration: if the attached Document is replaced, is the thumbnail rebuilt eagerly, lazily on next read, or left stale until something asks?
- Is a thumbnail settable by hand (paste an image on an item), or only ever derived from an attachment?

## Test cases

1. **Sample A** — the clean case: 10 rows, one image each, expect 10 attachments.
2. **Sample B** — the awkward case: 30 rows, two rows with two images, three `.emf` files to skip.
3. Re-import the same file twice — confirm the chosen re-import policy holds and no duplicate Documents accumulate.
4. A sheet with no images at all — must not error or change the text-import behaviour.
5. A row that is `Blocked` or `Error` in validate — must get no image attached, since no entity was written.

For thumbnails specifically:

6. A grid bind over a few hundred rows with thumbnails — confirm the payload does not carry image bytes and the images arrive as separate cached requests.
7. An entity with no thumbnail — the column renders empty, with no failed request per row.
8. Delete an entity — its thumbnail row goes with it (the `ON DELETE CASCADE`), and a workspace purge leaves nothing behind.

## Related

- `LibraryImportService`, `ImportUpsertService`, `ImportBatchService` — the Configuration 4 import chain.
- `IDocumentService` — file storage and entity linking.
- The Attached File tab, which is where the attached images will surface.
- `EntityDefinitionBaseColumns` and `EntityTreeGridWebApi.razor` — the column catalog and the `IconCss` template the thumbnail column is modelled on.
- The workspace purge invariant — any new tenant table must be purge-reachable.
