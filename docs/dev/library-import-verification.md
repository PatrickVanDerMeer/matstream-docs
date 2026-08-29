# Verification script — Library Items import (Configuration 4)

**Status:** not yet run. Written 2026-08-28.
**Why this exists:** the import chain has been exercised for Insert and Skip only. The Update path
has never run against a real entity, which means the merge guarantee — the whole reason
`ImportUpsertService` was extracted — is unproven, and so is the shared upsert's effect on the
MatStreamConnect publish path.

Run the tests in order. Test 1 sets up state that tests 2 and 3 reuse.

## What each test proves

| # | Test | Risk it closes |
|---|---|---|
| 1 | Update-path commit + merge guarantee | An import silently wiping properties set by hand or by another profile |
| 2 | Revision on update, and the Clear-when-empty flag | A mapped Revision column previewing as "Skip" and writing nothing; a blank cell clearing (or failing to clear) the wrong way |
| 3 | Lifecycle Blocked path | A released item being overwritten by an import |
| 4 | Ambiguous key | A duplicate key silently updating the wrong library item |
| 5 | Connect re-publish | The `ImportUpsertService` extraction regressing MatStreamConnect |

## Before you start

Use the workspace that already holds the 7 imported rows. Note its `WorkspaceID` — every query
below takes it as `@Wid`.

The profile needs at least one **mapped** property column (Description will do) and at least one
property that is **NOT** mapped. If every property in the category is mapped, unmap one first —
without an unmapped property there is nothing for test 1 to prove.

For test 2, map a column to the **Revision Number** field on the profile (target key `sys_Revision`).
Leave every *Clear when empty* checkbox **off** to start with — that is the default, and test 1
assumes it.

---

## Test 1 — Update path and the merge guarantee

The one that matters. Everything else is cheaper to fix than a silent data loss.

### 1.1 Plant a canary

Pick one imported item. In the app, open it in the **Property Browser** and set an **unmapped**
property to `MERGE-CANARY-1`. Save, and confirm the value shows after a reopen.

### 1.2 Record the baseline

```sql
DECLARE @Wid int = <WID>;
DECLARE @Name nvarchar(200) = N'<the item name>';

-- Every version of the item, oldest first.
SELECT e.EntityID, ev.EntityVerID, ev.EV_Version, ev.EV_Revision, ev.EV_Name,
       ls.LS_Name AS StateName, ev.EV_ModifiedUTC
FROM dbo.tbl_Entity e
JOIN dbo.tbl_EntityVer ev ON ev.EV_EntityID = e.EntityID
LEFT JOIN dbo.tbl_LifecycleState ls ON ls.LifecycleStateID = ev.EV_LifecycleStateID
WHERE e.E_WorkspaceID = @Wid AND ev.EV_Name = @Name
ORDER BY ev.EV_Version;
```

Note the `EntityID` and the highest `EV_Version`. Then the property values on the latest version:

```sql
DECLARE @EntityId int = <EntityID from above>;

SELECT pd.PD_Name, pv.PV_String255, pv.PV_StringMAX, pv.PV_Decimal, pv.PV_Int
FROM dbo.tbl_Entity e
JOIN dbo.tbl_EntityVer ev ON ev.EntityVerID = e.E_LatestVerID
JOIN dbo.tbl_PropertyValue pv ON pv.PV_EntityVerID = ev.EntityVerID
JOIN dbo.tbl_PropertyDef pd ON pd.PropertyDefID = pv.PV_PropertyDefID
WHERE e.EntityID = @EntityId
ORDER BY pd.PD_Name;
```

`MERGE-CANARY-1` must be in that list. Keep the result — it is the before picture.

### 1.3 Change exactly one mapped cell

In the Excel file, change **one** mapped cell on **that one row** — Description is the obvious
choice. Change nothing else, and do not touch the key columns.

### 1.4 Validate

Expected: that row reads **Update**. Every other row reads **Skip** (message "Already up to date -
nothing to write"). The counters read 0 new / 1 to update / 6 unchanged.

> If the row reads **Insert**, the key match failed — the item was not found. Check that the key
> column still holds the value it was imported with, and re-read the `Ambiguous` note in test 4.

### 1.5 Commit

Expected: 0 inserted / 1 updated / 6 unchanged, no errors.

### 1.6 Verify the write — this is the assertion that matters

Re-run both queries from 1.2.

- The mapped property you changed holds the **new** value.
- **`MERGE-CANARY-1` is still there.** If it is gone or blank, the merge guarantee is broken and
  nothing else in this script matters — stop and report it.
- Every other property is unchanged.
- Version count: **one** new `EV_Version` under an *Every change* policy, or the same version with a
  later `EV_ModifiedUTC` under *On editor change* when you are the same user who last saved. Either
  is correct; **more than one new version for one row is not.**
- The six untouched rows gained **no** versions.

```sql
-- Nothing but the edited row should have moved.
SELECT ev.EV_Name, MAX(ev.EV_Version) AS LatestVersion, MAX(ev.EV_ModifiedUTC) AS LastModified
FROM dbo.tbl_Entity e
JOIN dbo.tbl_EntityVer ev ON ev.EV_EntityID = e.EntityID
WHERE e.E_WorkspaceID = @Wid
  AND e.EntityID IN (<the 7 EntityIDs>)
GROUP BY ev.EV_Name
ORDER BY LastModified DESC;
```

### 1.7 Re-import unchanged

Run validate again on the same file without editing anything. Expected: 0 new / 0 to update /
7 unchanged, and the Import button reads "Nothing to import". This proves the update did not leave
the entity in a state that looks perpetually dirty.

---

## Test 2 — Revision as an updatable field

New behaviour (2026-08-28). Revision is written on create **and** on update.

### 2.1 Revision-only change

With the Revision column mapped, set that one row's revision to a value the item does not have —
`B`, say — and change **nothing else**.

Validate. Expected: the row reads **Update**.

> A **Skip** here is the specific bug this change fixed: `WouldChangeAsync` compares the name and the
> mapped *properties*, and Revision is neither. If it reads Skip, the revision argument is not
> reaching it.

Commit, then:

```sql
SELECT ev.EV_Version, ev.EV_Revision, ev.EV_ModifiedUTC
FROM dbo.tbl_Entity e
JOIN dbo.tbl_EntityVer ev ON ev.EV_EntityID = e.EntityID
WHERE e.EntityID = @EntityId
ORDER BY ev.EV_Version;
```

Expected: the latest version carries `EV_Revision = 'B'`, and the *previous* version still carries
the old revision — the history is intact.

### 2.2 Clear when empty — the per-mapping flag

What an empty cell means is a **per-mapping decision**, not a global rule: each field mapping
carries a *Clear when empty* checkbox (`IDM_BlankClears`), off by default. Test both positions on
the revision mapping, then the same pair on a property.

**First, that the flag round-trips.** Tick *Clear when empty* on the Revision mapping, save the
profile, close and reopen it. The tick must still be there — it crosses the DTO, the controller
mapping and the INSERT, so a value that does not come back means one of those three is dropping it.

```sql
SELECT m.IDM_SourceColumn, m.IDM_TargetField, m.IDM_IsKey, m.IDM_BlankClears
FROM dbo.tbl_ImportDefMap m
JOIN dbo.tbl_ImportDef d ON d.ImportDefID = m.IDM_ImportDefID
WHERE d.ID_WorkspaceID = @Wid AND d.ID_Name = N'<profile name>'
ORDER BY m.IDM_SortOrder;
```

**2.2a — unticked: blank leaves the value alone.** With the flag OFF, clear that row's revision cell
and leave the column mapped. Validate. Expected: **Skip**, and `EV_Revision` is still `B` after any
commit.

**2.2b — ticked: blank removes the value.** Turn the flag ON, same blank cell. Validate. Expected:
**Update**, and after commit `EV_Revision` is **NULL** on the latest version — while the previous
version still carries `B`, so the history keeps it.

**2.2c — the same pair on a property.** This is the case the flag was really asked for. Pick a
mapped property, blank its cell for one row, and run both positions of the flag: unticked leaves the
current value, ticked deletes it. Verify with the property query from 1.2 — a cleared property has
**no row at all** in `tbl_PropertyValue`, rather than a row holding an empty string.

**2.2d — a mapped column the file does not have.** Remove the mapped column from the sheet entirely
(or point a mapping at a column name the file lacks), with *Clear when empty* ticked, and import.
Expected: **nothing is cleared** on any row. A missing column is a profile/file mismatch, not an
instruction to wipe a value out of every row — the importer only ever treats a column that is
*present and blank* as a clear. If this one fails, every other blank-clears test is unsafe.

### 2.3 Revision on insert

Add a brand new row to the sheet with a revision value, and check that the created item is born with
it. Before this change, a non-key Revision map was invisible to the create path on this import and
`EV_Revision` came out null.

---

## Test 3 — the lifecycle Blocked path

### 3.1 Make the item unmodifiable

Two routes. Either move the item to a state whose `LSP_Modify` is denied for your group, or add that
denial to the state it is already in — Manage → Lifecycles → the state → Permissions.

Remember the convention: **a state with zero permission rows stays writable.** A state needs at
least one row before any denial means anything.

```sql
-- What the guard will actually read.
SELECT ls.LS_Name, ls.LS_IsFinal, p.LSP_UserGroupID, p.LSP_Modify, p.LSP_Read, p.LSP_Delete
FROM dbo.tbl_LifecycleState ls
LEFT JOIN dbo.tbl_LifecycleStatePerm p ON p.LSP_LifecycleStateID = ls.LifecycleStateID
WHERE ls.LifecycleStateID = <the state id>;
```

### 3.2 Validate and commit

Change a mapped cell for that row so it would otherwise be an Update, then validate.

Expected: the row reads **Blocked**, with the message

> Item is in '&lt;state&gt;' and cannot be modified.

That string is asserted on deliberately — the import log prints it verbatim. Commit, and confirm
nothing was written: no new version, no changed property, and the row still reads Blocked in the log.

---

## Test 4 — ambiguous key

New guard (2026-08-28). A key matching more than one entity is refused rather than resolving to
whichever row came back first.

**Set-up note:** if your key is the Name field you cannot build this case by hand — a Definition
category already refuses a duplicate name with a 409. Use a profile whose key is a **property**
(Part Number is the natural one) and create a second library item, different name, same Part Number.

Validate. Expected: that row reads **Ambiguous**, message "Key matches 2 existing library items -
cannot choose one", and it counts towards the error total. Commit writes nothing for it.

Delete the duplicate afterwards, or the rest of the script will keep tripping on it.

---

## Test 5 — MatStreamConnect re-publish

The regression check. Connect and the library import now share one upsert, and Connect is the path
with no dry run and an **unscoped** key lookup.

1. Publish a drawing that **already exists** in the library, so it takes the Update path.
2. Expected: success, mapped properties updated, and any property Connect does not map still holding
   its value — the same merge guarantee as test 1, through the other door.
3. Repeat against an item in a state that denies modify. Expected: a **403** carrying the state
   message, not a 500.

Watch for one specific new failure: `Key matches N existing items - cannot choose one`. On Connect
that is not a regression — it is the new guard finding a genuine duplicate that was previously being
resolved silently. If it fires, the fix is to scope Connect's lookup to its target category, which is
a change that has deliberately not been made yet.

---

## Reading the batch tables directly

When the log grid and the database appear to disagree, the database is the authority:

```sql
DECLARE @Wid int = <WID>;

SELECT TOP 5 ImportBatchID, IB_FileName, IB_Status, IB_TotalRows,
       IB_InsertCount, IB_UpdateCount, IB_SkipCount, IB_ErrorCount, IB_CommittedUTC
FROM dbo.tbl_ImportBatch
WHERE IB_WorkspaceID = @Wid
ORDER BY ImportBatchID DESC;

DECLARE @BatchId bigint = <ImportBatchID from above>;

SELECT IBR_RowNumber, IBR_ExternalKey, IBR_Action, IBR_TargetEntityID, IBR_ErrorText
FROM dbo.tbl_ImportBatchRow
WHERE IBR_ImportBatchID = @BatchId
ORDER BY IBR_RowNumber;
```

`IBR_Action` is the verdict validate left on the row, and commit honours it: anything that is not
`Insert` or `Update` is carried through untouched and nothing is written for it.
