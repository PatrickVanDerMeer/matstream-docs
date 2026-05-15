# Search

MatStream uses a Lucene-based full-text index for entity search in the Library and Explorer. This page documents how search queries are interpreted, what input variations are tolerated, and how to get reliable results.

---

## How the search index works

When an entity is created or updated, its name, number, and key property values are indexed by the MatStream Tenant API using **Apache Lucene.NET**. Searches run against this index — not directly against the SQL database — which makes them fast even across large catalogues.

The index uses a custom analyzer called `MatStreamAnalyzer` that is designed specifically for engineering part numbers and identifiers.

---

## Separator normalisation

Engineering identifiers often use different separator characters interchangeably — dots, dashes, underscores, and spaces may all appear in the same part number depending on the source system or user habit.

`MatStreamAnalyzer` normalises all of these separators to nothing (removes them) before indexing and before query evaluation. This means the following queries all find the same entity:

| Query | Matches |
|---|---|
| `SS-42S4` | ✅ |
| `SS-42-S4` | ✅ |
| `SS 42 S4` | ✅ |
| `SS42S4` | ✅ |

The characters normalised as separators are: `.` (dot), `-` (dash), `_` (underscore), and ` ` (space).

---

## Prefix and substring matching

At index time, `MatStreamAnalyzer` generates **edge n-grams** — every prefix from length 1 up to 30 characters is stored as a token. This means typing any prefix of an identifier finds it immediately, without needing to type the full string.

Examples for the identifier `HTC.000.000.001` (normalised to `htc000000001`):

| Query | Finds `HTC.000.000.001`? |
|---|---|
| `HTC` | ✅ (prefix match) |
| `htc000` | ✅ (prefix match) |
| `HTC.000.000` | ✅ (separators normalised, then prefix match) |
| `HTC.000.000.001` | ✅ (exact match) |
| `000001` | ❌ (not a prefix of the normalised form) |

!!! note "Substring vs prefix"
    The index uses **edge n-grams**, which are prefixes — not arbitrary substrings. Searching for a segment that appears in the middle of an identifier (e.g. searching `000` to find `HTC.000.000.001`) will not match. Search from the start of the identifier for reliable results.

---

## Case sensitivity

Searches are **case-insensitive**. `HTC`, `htc`, and `Htc` all return the same results.

---

## Query-time behaviour

At query time, the same separator normalisation and lowercasing is applied — but **no n-gram expansion**. The typed query is matched as-is (after normalisation) against the n-gram tokens stored at index time.

This means:
- Type `htc000` → matched against stored tokens `h`, `ht`, `htc`, `htc0`, `htc00`, `htc000`, … → **match found**
- Type `htc000` in mixed case → normalised to `htc000` → same result

---

## Indexed fields

The following fields are included in the Lucene index per entity:

| Field | Notes |
|---|---|
| Entity number (EV_Name) | Always indexed |
| Entity name / description | Always indexed |
| Key property values | Properties marked as searchable in the category definition |

Properties not marked searchable in the category configuration are not indexed and cannot be found via the search bar.

---

## When to rebuild the index

The Lucene index is updated automatically on entity create/update. A manual index rebuild may be needed after:

- Bulk data imports
- Schema migrations that affect indexed fields
- Restoring a database backup

Workspace administrators can trigger an index rebuild from workspace settings (if enabled).

---

## Search vs SQL filtering

The quick search bar uses the Lucene index. Advanced filter panels (property-based filters, date ranges, lifecycle state filters) run as SQL queries against `tbl_PropertyValue` and `tbl_EntityVer`, not via Lucene. Complex queries combining both are executed as: Lucene for text matching → SQL for additional filtering on the result set.

---

## Related

- [Library](../features/library.md) — the primary search surface
- [Explorer](../features/explorer.md) — structure-based browsing
- [Property definitions](../admin/properties.md) — marking properties as searchable