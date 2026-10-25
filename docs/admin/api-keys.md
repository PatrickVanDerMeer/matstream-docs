# API keys

API keys allow external systems — such as the Autodesk Vault sync 
plugin, a webshop integration, or an ERP connector — to access 
MatStream using integration credentials bound to a user email address.
The associated user must have the required workspace access and permissions.

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
workspace for management. Use the intended workspace and a user with the
appropriate permissions when configuring an integration.

---

## Creating an API key

To create an API key:

1. Navigate to **Administration → API keys**
2. Click **New API key**
3. Enter a descriptive name for the key — for example, *Vault sync plugin* 
   or *ERP integration*
4. Enter the user email address to bind to the key
5. Optionally set an expiry date
6. Click **Save**

Creation returns a **client ID** and **client secret**. Copy the secret
immediately; it is only returned at creation. Your integration also needs
the bound user email to request an access token.

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

Revoking a key prevents it from being used to request new access tokens.
Previously issued access tokens can remain usable until they expire.
Update integrations with replacement credentials before revoking an old key.

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

- [API authentication](../api/authentication.md) — exchange credentials for an access token
- [First API request](../api/first-request.md) — connect using PowerShell

- [Users & groups](users-groups.md) — manage user access and permissions
- [Syncing with Vault](../vault-integration/sync.md) — configure the 
  Autodesk Vault integration using an API key