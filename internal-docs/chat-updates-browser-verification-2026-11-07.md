# Chat updates: local verification, 7 November 2026

## Environment

- Assembly: `https://localhost:7053`
- Manage: `https://localhost:7081`
- Tenant API: `https://localhost:7239`; Core API: `https://localhost:7273`
- Workspace: **Whole Order Browser Test**, slug `5z8phz`, numeric ID 103.
- The account's one-free-workspace limit prevented a separate workspace. Existing test workspace
  used instead; new manual test records have the `CHAT-20261107` prefix.
- HTTP Assembly at port 5055 failed login because Core CORS allows the HTTPS origin. Switching
  to the configured HTTPS origin resolved it; the user performed both sign-ins.

## Verified in the browser

| Update | Result and evidence |
|---|---|
| Setup and Supplier/Client split | Engineering configuration/sample data and Sales Orders configuration/sample data completed in 289 seconds. Finalizer created Supplier and Client; Acme, Meridian, Northline and Vulcan moved to Supplier, Northwind to Client. Existing IDs/addresses retained. |
| Supplier picker | Add from Library on Suppliers showed exactly the four Supplier records and no Client/Company. |
| Thumbnail paste | Right-click menu exposes Edit, Delete and Paste Image from Clipboard. Pasting a generated blue PNG, saving and reopening preserved the image. |
| Ordering Text load | Opened without the previous template-loading error. Saving a template succeeded. |
| Ordering Text variables | Dropdown included Description, Material and other available properties. Inserting Material appended `{{Material}}`. |
| Default Stock Unit | New Part `CHAT-20261107-Stock-Default` showed pc before assignment; save persisted UoQ ID 1. Administrator UI exposes available units and No default. Test change to kg was restored to pc. |
| Equipment Material | Configuration → Equipment → Properties contains Material. |
| RVD pricing regression | Copied item's RVD tab loaded its Material Certificates requirement without a pricing 404. |
| PDF double-click | Checked personal setting opened existing `023.713.000.pdf` in the Open PDF viewer. Explicit user/default controls are present. |
| Library copy/index | `CHAT-20261107-Library-Copy` appeared immediately through quick search after Copy Structure. No Properties to clear section. |
| Explorer copy/index | Project `CHAT-20261107-Explorer-Copy` and placement `CHAT-20261107-Explorer-Position` copied successfully. Fresh Library quick search returned exactly that placement, with parent Bill of Materials (BOM), project P-2026-0042 and the copied project's full path. |
| Documents context Paste | Browser clipboard PNG queued as `clipboard-1.png` in Add documents, requiring category selection and Upload. Cancel left it unuploaded. |
| Documents Ctrl+V | Same PNG queued as `clipboard.png` in upload review. Cancel left it unuploaded. Ordinary text editors are excluded by the handler. |
| Address naming | New Company Address offers Head Office, Warehouse, Delivery Address, Invoice Address and Branch Office, plus custom text. Created `CHAT-20261107 Address` under migrated Supplier Acme. |
| Address defaults | New address showed Office and Netherlands in combo boxes. Saved it. |
| Editable price date | After applying missing migration, Suppliers displayed a date picker. Price-only save stamped current date. Calendar selection of 2026-11-06 plus Unit Price 14.35 in the same save persisted both values. |
| Editable price script | Configuration → Automations exposes Supplier: Set Price Updated On, RunOnSave and DatePropertyDefId. Entered a test comment in the script editor and cancelled, preserving the saved script. |
| Occurrence visibility/context | Library category filter includes Company Address and BOM Position. Address results show their owning party; Explorer BOM result shows parent, project and path. |
| Administrator occurrence scope | BOM Position initially visible with Explorer-owned scope. Saved Library-owned scope, reopened and confirmed value 1, then restored Explorer-owned value 2. Company Address is visible with All scope. |
| Sample Data filter | After restart, Advanced filters → Sample Data → Equals → True returned 52 records and excluded the manually created CHAT records. |
| Quick search after advanced filters | Closed advanced filters, entered CHAT-20261107-Library-Copy and searched; exactly one result appeared with the entered query retained. |
| Versioned supplier rows | Reopened version 4 of the Library copy after API restart. Exactly one Meridian supplier row appeared; Unit Price 14.35 and manual date 2026-11-06 remained intact. |
| Open Manage | After Assembly restart, clicking Open Manage created a separate browser tab at https://manage.matstream.com/. No production sign-in or mutation was performed. |

## Issues found and repaired

1. **Open Manage hostname:** original requested spelling `manage.mastream.com` failed to load.
   Corrected the button to `https://manage.matstream.com`, preserving new-tab/no-opener behavior.
2. **Missing local price-date migrations:** the running database still had CP_IsReadOnly = 1
   and `tr_PropertyValue_SupplierPriceDate`. Applied the prepared editable-date and script
   migrations locally. Added QUOTED_IDENTIFIER ON so the editable-date migration also works
   through sqlcmd with indexed/computed-column requirements. Manual date plus price now works.
3. **Repeated Uses rows after versioning:** SearchUses joined every historical DefinitionLink.
   Changed it to select one link belonging to the displayed occurrence version, preferring the
   version-specific link over legacy unversioned links. SQL regression covers old/current/legacy links.
4. **Boolean filter encoding:** Query Builder sends true/false, while SQL bit text is 1/0.
   Normalize Boolean comparison values only for stored Boolean rows. Regression covers
   true/True/1, false/0, and an ordinary text property containing literal `true`.
5. **Quick-search text during filter commit:** keep a local copy of the submitted text before
   committing builder rules, whose synchronous Changed event can otherwise restore the old query.

## Browser limitations

- User rebuilt/restarted the local apps. The repaired Sample Data search, quick-search state,
  supplier row uniqueness and Manage target were retested successfully against the restarted apps.
- Follow-up in normal Google Chrome: selected `clipboard-test.png` (185 bytes) in Windows
  Explorer and copied it with Ctrl+C. Ctrl+V in ITM-00001's Documents grid opened Add documents
  with the original filename and size. Cancelled review without uploading. Evidence:
  `chrome-explorer-ctrl-v.png`. Context-menu Paste reached Chrome's clipboard permission prompt;
  the user allowed access. The same file was not exposed to menu Paste, and MatStream displayed
  its fallback guidance to use Ctrl+V or Add File. Evidence: `chrome-explorer-menu-paste.png`.
  Thus Ctrl+V passed; menu Paste did not meet the upload-review outcome for this Explorer file
  in Chrome. No upload was committed. Non-image shell files have not yet been exercised.
- Personal-false versus workspace-true PDF precedence, seed rerun preservation, and scope/access
  combinations have automated coverage; they were not exhaustively exercised with multiple
  signed-in users in the browser.
- Thumbnail Edit/Delete menu visibility was checked; the saved paste path was exercised.
  Image deletion was not committed during this run.

## Automated verification

- API full normal suite: **869 passed, 93 skipped** (integration opt-ins remain skipped).
- Manage suite: **208 passed**.
- Focused suite with local SQL opt-ins: **27 passed**, including scope/permissions, defaults,
  supplier date scripts/migration, copy indexing and requested-column regressions. Counts overlap
  the normal suite; do not add them as distinct tests.
- Assembly isolated build passed with the existing RibbonTooltipSettings warning.
- Help: `python -m zensical build --clean` passed without issues.
- Builds ran in `test-artifacts/2026-11-07-chat-updates/build` and `web-build` because running
  API processes lock their usual output DLLs.

## Local test artifacts

Screenshots and the clipboard PNG are in `test-artifacts/2026-11-07-chat-updates` beside the repos.
Useful images: `pdf-viewer.jpg`, `thumbnail-pasted-saved.jpg`, `ordering-text-variables.jpg`,
`documents-paste-review.jpg`, `address-defaults.jpg`, `default-stock-unit-pc.jpg`,
`editable-price-script.jpg`, `library-copy-indexed.jpg`, and `explorer-copy-indexed.jpg`.
Restart verification adds `sample-data-filter-passed.jpg` and `supplier-date-preserved.jpg`.
The older `sample-data-filter.jpg` was captured after closing the builder (which clears rules);
use `sample-data-filter-passed.jpg` as filter evidence.

Reviewable records left in workspace 103:

- `CHAT-20261107-Library-Copy` — entity 15065; its independently copied supplier occurrence is 15067.
- `Material Certificates (EN 10204 3.1) (copy)` — copied requirement 15068.
- `CHAT-20261107 Address` — entity 15118, under Acme Pumps Ltd (14919).
- `CHAT-20261107-Stock-Default` — entity 15119.
- `CHAT-20261107-Explorer-Copy` and its copied descendants, including the uniquely named placement.
- A workspace text template named **Ordering Text Template**, created while exercising template save.

No production deployment or help publication was performed.
