# Test script — stock reservations & purchase-order cancel

Covers everything built 2026‑08‑16/17. Run top to bottom in a **new workspace with sample data**.

Replace `<wid>` with the new workspace id throughout.

> **Before you start, in this order:**
> 1. Run `MatStream.API/Database/20260818_allow_multiple_final_lifecycle_states.sql` — **before**
>    creating the workspace, or the Purchase Order lifecycle fails again on `Cancelled`.
> 2. Restart the API.
> 3. Create a **fresh** workspace — discard the partially-seeded one rather than re-running over it.
>
> Also make sure
> `MatStream.API/Database/20260817_fix_materialize_children_version_filter.sql` has been run against
> this database. It is a stored procedure, so it is database-wide — already done if you are on the
> same DB as yesterday, needed again only on a fresh database.

---

## 0. Seeding — is the new config actually there?

```sql
-- Stock location model: only the leaf carries the behaviour
SELECT C_Name, C_FamilyTypeID, C_BehaviorTypeID
FROM   dbo.tbl_Category
WHERE  C_WorkspaceID = <wid> AND C_Name IN ('Warehouse','Rack','Storage Location');
```
✅ `Storage Location` → family **8**, behavior **4**. Warehouse and Rack → behavior **NULL**.

```sql
-- The two new lifecycle states
SELECT ld.LD_Name, ls.LS_Name, ls.LS_IsFinal
FROM   dbo.tbl_LifecycleState ls
JOIN   dbo.tbl_LifecycleDef   ld ON ld.LifecycleDefID = ls.LS_LifecycleDefID
WHERE  ld.LD_WorkspaceID = <wid>
  AND  ls.LS_Name IN ('Covered from Stock','Cancelled');
```
✅ `Covered from Stock` on **Material Requirement Lifecycle**, `Cancelled` on **Purchase Order
Lifecycle** — and `Cancelled` should be **IsFinal = 1**, now that the one-final index is gone.

```sql
-- The pre-existing bug the index was hiding: Document Lifecycle should now have BOTH finals
SELECT ls.LS_Name, ls.LS_IsFinal
FROM   dbo.tbl_LifecycleState ls
JOIN   dbo.tbl_LifecycleDef   ld ON ld.LifecycleDefID = ls.LS_LifecycleDefID
WHERE  ld.LD_WorkspaceID = <wid> AND ld.LD_Name = 'Document Lifecycle'
ORDER  BY ls.LS_SortOrder;
```
✅ Both `Released` and `Obsolete` present, both final. Before the index was dropped, `Obsolete` could
never be created and the Document feature stopped early.

```sql
-- Stock-control properties
SELECT PD_Name FROM dbo.tbl_PropertyDef
WHERE PD_WorkspaceID = <wid>
  AND PD_Name IN ('Is Stock Item','Minimum Stock Qty','Maximum Stock Qty');
```
✅ Three rows.

**In the app:** `Warehouse` should contain `Rack 01` and `Rack 02`, each with three Storage Locations (`01-A`…`02-C`).

---

## 1. BOM shows each child once

Navigate to `Projects/2026/Sample Engineering Project/Bill of Materials (BOM)` and expand
`0023.0713.101`.

✅ **14 children** — 1 drawing, 5 parts, 8 welds. Each name appears **once**.

❌ If each appears 5×, the materialize proc fix is missing on this database.

---

## 2. Stock tab on a Part

Open Part `0761.1161.120` → **Stock** tab.

✅ Totals strip: In Stock 0 / Reserved 0 / Available 0 / On Order 0
✅ "This item is not stocked at any location."
✅ Three settings: Is Stock Item, Minimum Stock Qty, Maximum Stock Qty

Tick **Is Stock Item**, set Min `1`, Max `4`.

✅ "Unsaved — use Save to commit" appears, and the footer changes to **Cancel / Save and Close**
✅ Save, reopen the item → values persisted
✅ The same three values also appear on the **General** tab

> ⚠️ If the footer stays on **Close** when you edit, the dirty flow is broken — stop and report.

---

## 3. Post stock

`Warehouse/Rack 01/01-A` → **Stock** ribbon → **Add Item** → `0761.1161.120` → **Adjust** → On Hand `10`.

```sql
SELECT SP_LocationEntityID, SP_OnHandQty, SP_ReservedQty
FROM   dbo.tbl_StockPosition WHERE SP_WorkspaceID = <wid>;
```
✅ One row, real location, on-hand 10.

Reopen the Part's **Stock** tab → In Stock **10**, Available **10**, grid shows `01-A  10`.

---

## 4. Raise a requirement

Project BOM → select the **BOM Position** row `0761.1161.120` → **Change State → Ready for Procurement**.

Open `Purchasing/Pending Material Requirements (MR)`.
Add columns: **In Stock**, **Open Qty**, **Reserved Qty**, **Required Qty**.

✅ One line, Required 3, In Stock **10**, Open Qty **3**, Reserved **0**, state *Requested*

❌ In Stock blank → the item-stock enrichment is not firing.
❌ Open Qty blank → the coverage enrichment is not firing.

---

## 4b. The definition revision is pinned

Raising the requirement should freeze which revision was requisitioned — on the **occurrence** and on
the **MR line**.

```sql
SELECT l.EL_EntityParentID, l.EL_EntityID, l.EL_EntityVerID, l.EL_VerRefModeID,
       ev.EV_Name AS PinnedOn
FROM   dbo.tbl_EntityLink l
JOIN   dbo.tbl_Entity     e  ON e.EntityID    = l.EL_EntityParentID
JOIN   dbo.tbl_EntityVer  ev ON ev.EntityVerID = e.E_LatestVerID
WHERE  l.EL_LinkTypeID = 4 AND l.EL_EntityVerID IS NOT NULL;
```
✅ Two rows — the BOM Position occurrence and the MR line — each with `EL_VerRefModeID = 3` (Pinned)
and a non-null `EL_EntityVerID`.

❌ No rows means nothing pinned: before this change every DefinitionLink followed latest forever.

> **Expected side effect:** the occurrence's BOM is now frozen at that revision. Revise the Part's
> BOM afterwards and the occurrence keeps showing the requisitioned structure; a line dropped from
> the Library BOM stays visible flagged as orphaned rather than disappearing. That is deliberate.

---

## 5. Reserve — partial

Select the line → **Procurement** → **Reserve from Stock**.

✅ **No location dropdown**
✅ Totals: In Stock 10 / Reserved 0 / Available 10
✅ Quantity pre-filled **3** and labelled "(3 still needed)" — *not* 10
✅ "Held at — 01-A — 10 on hand", with the note about picking

Change the quantity to **2** and Reserve.

✅ Grid: Reserved **2**, Open Qty **1**, In Stock 10
✅ The line **stays** in Pending MR — it is only partly covered

```sql
SELECT SP_LocationEntityID, SP_OnHandQty, SP_ReservedQty
FROM   dbo.tbl_StockPosition
WHERE  SP_WorkspaceID = <wid> AND SP_ItemEntityID = <part entity id>;
```
✅ **Two rows**: `01-A` on-hand 10 / reserved **0**, and a row with **NULL** location / reserved **2**

> This is the core of the item-level model. If the 2 sits on `01-A`, the location-free path is not
> being taken.

---

## 6. Reserve dialog totals (the LEFT JOIN fix)

Open **Reserve from Stock** again, then Cancel.

✅ Totals now read **Reserved 2 / Available 8**

❌ "Reserved 0 / Available 10" means `GetByItemAsync` is still inner-joining the location — and the
server's over-reservation guard is reading zero too.

---

## 7. Button gating

With Open Qty **1**:
✅ Create Purchase Order — enabled
✅ Reserve from Stock — enabled
✅ Remove Reservation — enabled

---

## 8. Remove Reservation

**Remove Reservation** → proposes **2** ("2 reserved for this requirement") → remove **1**.

✅ Reserved **1**, Open Qty **2**

```sql
SELECT SLG_MovementType, SLG_ReservedDelta, SLG_CreatedUTC
FROM   dbo.tbl_StockLedger WHERE SLG_WorkspaceID = <wid> ORDER BY StockLedgerID DESC;
```
✅ Newest row: movement **8**, delta **-1**. The earlier `7 / +2` row is still there — the ledger
records both movements.

---

## 9. Full coverage removes the line from the inbox

Reserve the remaining **2** (Open Qty → 0).

✅ Reserved 3, Open Qty 0
✅ **The line disappears from Pending Material Requirements**

Find it under `Projects/2026/Sample Engineering Project`.

✅ Its state is **Covered from Stock**
✅ Create Purchase Order and Reserve from Stock are **disabled**; Remove Reservation is **enabled**

---

## 10. Reopening puts it back

Select the line → **Remove Reservation** → remove **1**.

✅ State returns to **Requested**
✅ It reappears in Pending Material Requirements with Open Qty **1**

---

## 11. Purchase order uses the open quantity

Remove the remaining reservations so Open Qty is **3**, then reserve **1** again (Open Qty **2**).

Select the line → **Create Purchase Order** → confirm.

✅ The PO orders **2**, not 3
✅ The line leaves the inbox, state **Ordered**

```sql
-- The PO line pins the revision that was REQUISITIONED, not the definition's latest
SELECT EL_EntityParentID, EL_EntityID, EL_EntityVerID, EL_VerRefModeID
FROM   dbo.tbl_EntityLink WHERE EL_LinkTypeID = 4 AND EL_VerRefModeID = 3;
```
✅ A third pinned row appears — the PO line — carrying the same `EL_EntityVerID` as the MR line.

Then reserve enough to cover a line fully and try to order it:

✅ Reported as skipped — *"Fully covered from stock — 3 of 3 reserved."*
✅ **No empty purchase order is created**

```sql
SELECT e.EntityID, ev.EV_Name, ls.LS_Name
FROM   dbo.tbl_Entity e
JOIN   dbo.tbl_EntityVer ev ON ev.EntityVerID = e.E_LatestVerID
JOIN   dbo.tbl_Category  c  ON c.CategoryID   = ev.EV_CategoryID
LEFT JOIN dbo.tbl_LifecycleState ls ON ls.LifecycleStateID = ev.EV_LifecycleStateID
WHERE  e.E_WorkspaceID = <wid> AND c.C_BehaviorTypeID = 8;
```

---

## 12. Cancel a purchase order

Find the PO under `Purchasing/Open Purchase Orders` (or the Purchasing folder) and **select the order
header**.

✅ The **Procurement** ribbon tab appears (it did not for a PO header before)
✅ **Cancel Order** is enabled

Click it → confirm.

✅ Message reports lines cancelled and requirements returned
✅ The requirement is back in **Pending Material Requirements**, state **Requested**, Open Qty restored
✅ The purchase order still exists, state **Cancelled**

```sql
SELECT SLG_MovementType, SLG_OnOrderDelta, SLG_SourceEntityID
FROM   dbo.tbl_StockLedger WHERE SLG_WorkspaceID = <wid> ORDER BY StockLedgerID DESC;
```
✅ Movement **6** (OnOrderRelease) with a negative delta, no location.

```sql
SELECT EL_EntityParentID, EL_EntityID FROM dbo.tbl_EntityLink
WHERE EL_LinkTypeID = 6;
```
✅ The FulfillmentLink for that line is **gone** — otherwise the requirement cannot be re-ordered.

Re-order the reopened requirement to prove it: **Create Purchase Order** should now work rather than
saying *"Already covered by purchase order line X."*

---

## 13. Cancel refuses after a receipt

Create a PO for an open requirement, then **Receive** it (Procurement → Receive, into `01-A`).

Select the order → **Cancel Order**.

✅ Nothing is cancelled; the message reads *"… already received — a line cannot be cancelled once
goods have arrived."*
✅ The order keeps its state; stock stays on hand

---

## 14. Cancel a single line

Create a PO covering **two** requirements (raise a second BOM position first), then select **one PO
line** and Cancel Order.

✅ Only that line is cancelled; its requirement returns to the inbox
✅ The **order stays live** (not Cancelled) because the other line is still outstanding

---

## 15. Grid column details

Right-click any numeric column header (e.g. Required Qty) → **Column settings**.

✅ Header Text shows **Required Qty**, Mapped Field **RequiredQty** — *not* `DataItem.RequiredQty`
✅ A **Header Text Align** dropdown is present beside Text Align
✅ Column headers are left-aligned even where the values are right-aligned

Set Header Text Align to `right` on one column, then **resize a different column**.

✅ The setting survives — it is not wiped by the layout rebuild

---

## Known gaps (not bugs)

- **UoQ** is blank on a newly added BOM line until someone picks one — deliberate: a blank unit means
  "nobody chose one", not an implicit piece.
- **PO-line aggregates** (Ordered Qty etc. on the Open Purchase Orders filter) are still uncomputed —
  the same enrichment gap that was closed for MR lines.
- **Duplicate "Stock" tab** may appear on Part/Assembly if a tab already displays under that name.
- **No picking flow** exists yet: a reservation records *that* stock is committed, never which shelf
  it will come off.
