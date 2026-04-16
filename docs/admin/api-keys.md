# API keys

API keys allow external systems — such as the Autodesk Vault sync 
plugin, a webshop integration, or an ERP connector — to access 
MatStream without a personal user account.

---

## When to use API keys

Use an API key when an external system or automated process needs to 
read from or write to MatStream. Common use cases:

- **Autodesk Vault sync** — the Vault plugin uses an API key to 
  authenticate with MatStream and sync part data
- **ERP integrations** — an ERP system reads released BOM data from 
  MatStream via the API
- **Automation scripts** — a script that creates or updates entities 
  automatically

API keys are workspace-scoped — each key belongs to a specific 
workspace and can only access data within that workspace.

---

## Creating an API key

To create an API key:

1. Navigate to **Administration → API keys**
2. Click **New API key**
3. Enter a descriptive name for the key — for example, *Vault sync plugin* 
   or *ERP integration*
4. Optionally set an expiry date
5. Click **Save**

The key is displayed once immediately after creation.

!!! warning "Copy the key immediately"
    An API key is shown only once when created. Copy it immediately 
    and store it securely — for example in a password manager or 
    secrets vault. If you lose an API key, you must revoke it and 
    create a new one.

    Never store API keys in source code or configuration files that 
    are committed to version control.

---

## Revoking an API key

To revoke an API key:

1. Navigate to **Administration → API keys**
2. Find the key you want to revoke
3. Click **Revoke**

The key is invalidated immediately. Any system using that key will 
lose access until a new key is provided and configured.

---

## Rotating an API key

API keys do not rotate automatically. To rotate a key:

1. Create a new API key with the same name and settings
2. Update the external system with the new key
3. Verify the external system is working correctly with the new key
4. Revoke the old key

Rotate keys regularly — especially if you suspect a key may have 
been exposed.

---

## Next steps

- [Users & groups](users-groups.md) — manage user access and permissions
- [Syncing with Vault](../vault-integration/sync.md) — configure the 
  Autodesk Vault integration using an API key