# Syncing with Autodesk Vault

MatStream includes a Vault integration that synchronises parts, assemblies, and properties from Autodesk Vault into MatStream entities. The integration is an **upsert pipeline**: entities are created on first sync and updated on subsequent ones.

---

## Overview

The Vault sync is triggered by a Vault plugin (or any HTTP client) sending a POST request to the MatStream Tenant API. MatStream processes the payload, matches incoming records to existing entities by GUID, and creates or updates them in place.

```
Vault Plugin
  → POST /api/wid/{workspaceSlug}/vault/sync
      → GUID match → update existing entity
      → no match   → create new entity
      → parent-child links preserved
```

---

## How GUID matching works

Every MatStream entity stores a GUID field (`E_Guid`). This field maps to the Vault object's unique identifier. Because Vault GUIDs survive renames and moves, MatStream can reliably find the matching entity even if its number or name has changed since the last sync.

A unique index on `E_Guid` enforces that no two entities in the same workspace share a GUID.

---

## Upsert behaviour

| Scenario | What happens |
|---|---|
| Entity with matching GUID exists | Properties updated in place; no new revision unless entity is in a released state |
| No entity with this GUID | New entity created using the category's numbering scheme |
| Entity exists but in a released state | A new revision is created to hold the updated values |
| Entity present in MatStream but absent from payload | **Not deleted** — Vault sync is additive only |

---

## Sync payload

The sync endpoint accepts a JSON payload describing one or more entities and their relationships:

```json
{
  "entities": [
    {
      "guid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "number": "MAT-2024-0001",
      "name": "Bracket Assembly",
      "categoryKey": "Assembly",
      "parentGuid": null,
      "level": 0,
      "properties": {
        "PD_Description": "Main bracket assembly",
        "PD_Material": "Aluminium 6061"
      }
    },
    {
      "guid": "yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy",
      "number": "MAT-2024-0002",
      "name": "Side Plate",
      "categoryKey": "Part",
      "parentGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "level": 1,
      "properties": {
        "PD_Description": "Side plate, 3mm",
        "PD_Material": "Steel S235"
      }
    }
  ]
}
```

**Field reference:**

| Field | Required | Description |
|---|---|---|
| `guid` | Yes | Vault object GUID — used for matching |
| `number` | Yes | Human-readable identifier (used on create; ignored on update if entity already has a number) |
| `name` | Yes | Entity name / description |
| `categoryKey` | Yes | Key of the target MatStream category |
| `parentGuid` | No | GUID of the parent entity in the assembly tree |
| `level` | No | BOM depth level (0 = top-level) |
| `properties` | No | Map of property definition keys to string values |

---

## Assembly structure

Parent-child relationships are preserved during sync. The `parentGuid` field links a child entity to its parent. MatStream creates entity link records to represent the assembly tree, visible in the Explorer tree view and Uses/Where-Used tabs.

---

## Property sync

Property values in the `properties` map are written using the workspace's property definition keys. Type coercion is applied automatically:

- **Text** — written as-is
- **Numeric** — parsed from string; invalid values skipped with a warning
- **Date** — ISO 8601 format expected (`YYYY-MM-DD`)
- **Boolean** — `"true"` / `"false"` (case-insensitive)

Properties not defined in the workspace are silently ignored.

---

## API reference

| Method | Endpoint | Description |
|---|---|---|
| `POST` | `/api/wid/{slug}/vault/sync` | Submit a sync payload |

The response envelope includes per-entity results: `created`, `updated`, `skipped`, or `error` with a message.

---

## Setting up the Vault plugin

The Vault plugin is a separate component installed on the Vault server or a client machine with Vault access. It reads entity data from Vault and posts it to the MatStream sync endpoint.

**Configuration required:**

1. MatStream workspace slug and API base URL
2. An API key with write permissions (Configuration → API Keys)
3. Mapping of Vault categories to MatStream category keys
4. Mapping of Vault user-defined properties (UDP) to MatStream property definition keys

---

## Troubleshooting

**Entity created instead of updated:**
The GUID in the payload does not match any stored `E_Guid`. Check that the Vault plugin sends the correct Vault GUID.

**Properties not updating:**
Verify that property definition keys in the payload match those in MatStream (Configuration → Properties). Keys are case-sensitive.

**Category not found:**
The `categoryKey` must match the internal key of a category defined in the workspace. Check Configuration → Categories.

**401 Unauthorized:**
The API key is missing or revoked. Generate a new key in Configuration → API Keys.

---

## Related

- [Import Definitions](../features/import-definitions.md) — alternative import path using Excel files
- [MatStreamConnect](../integrations/matstream-connect.md) — desktop companion for Inventor BOM imports
- [API Keys](../admin/api-keys.md) — create and manage API keys for integration access
