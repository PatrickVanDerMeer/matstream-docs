# Internal API authentication

MatStream Core API and Tenant API communicate using asymmetric ECDSA P-256 request signatures. They do not share an internal secret.

This is service-to-service authentication. It is separate from the workspace API keys created under **Administration > API keys** for external integrations.

---

## Key ownership

A customer-hosted Tenant must be able to verify Core without receiving a secret that could impersonate Core. Every service therefore owns a separate key pair:

- **Core private key** stays on Core and signs Core-to-Tenant requests.
- **Core public key** is installed on each Tenant and verifies Core signatures.
- **Tenant private key** stays on that Tenant and signs Tenant-to-Core requests.
- **Tenant public key** is stored in CoreDb and verifies that Tenant.

Core has one key pair. Each independently deployed Tenant has one key pair. With one Core and two Tenant deployments, there are three key pairs in total.

Private keys are never exchanged. Compromising one Tenant key cannot be used to impersonate Core or another Tenant deployment.

---

## How a request works

1. The caller calculates a SHA-256 hash of the request body.
2. It signs the request details with its private key.
3. The receiver selects a public key using the signed service ID and key ID.
4. The receiver verifies the signature, audience, timestamp, nonce, route, query, method, and body hash.
5. Core obtains the Tenant deployment ID from the trusted key registration and limits workspace access to that deployment.
6. A valid request continues. Invalid authentication returns `401`; cross-deployment workspace access returns `403`.

Signatures are short-lived and each nonce can be accepted only once. HTTPS is still required because signatures do not encrypt request content.

---

## Generate key pairs

Generate keys outside the source repository. Protect private files with operating-system permissions or a secrets manager.

```powershell
openssl genpkey -algorithm EC -pkeyopt ec_paramgen_curve:P-256 -out core-private.pem
openssl pkey -in core-private.pem -pubout -out core-public.pem

openssl genpkey -algorithm EC -pkeyopt ec_paramgen_curve:P-256 -out tenant-private.pem
openssl pkey -in tenant-private.pem -pubout -out tenant-public.pem
```

Use unique keys and identities for development, test, production, and every independently hosted Tenant.

---

## CoreDb deployment registry

Apply these CoreDb migrations from the API repository in order:

1. `MatStream.Core.API/Migrations/20260803_AddTenantDeploymentSigning.sql`
2. `MatStream.Core.API/Migrations/20260803_RemoveLegacyWorkspaceDeploymentColumns.sql`

The final normalized structure is:

| Storage | Purpose |
|---|---|
| `tbl_TenantDeployment` | Tenant identity, expected audience, API base URL, hosting scope, enabled state |
| `tbl_TenantDeploymentKey` | Public keys, key IDs, validity dates, enabled state, and revocation date |
| `tbl_Workspace.W_TenantDeploymentID` | Foreign key assigning each workspace to one Tenant deployment |

Core loads Tenant public keys and outbound audiences from CoreDb. Adding or rotating a Tenant therefore does not change Core's `appsettings.json` and does not require a Core deployment.

`tbl_Workspace` intentionally does not store a Tenant API URL or hosting-server key. Many workspaces can reference one deployment, and Core obtains `TD_TenantApiBaseUrl` and `TD_HostingServerKey` through `W_TenantDeploymentID`. API responses can still contain `TenantApiBaseUrl`; it is a joined value rather than duplicated workspace data.

Register a Tenant deployment and its public key in one transaction:

```sql
BEGIN TRANSACTION;

INSERT dbo.tbl_TenantDeployment
    (TD_ServiceID, TD_Audience, TD_TenantApiBaseUrl, TD_HostingServerKey, TD_Enabled)
VALUES
    ('tenant-customer-production',
     'matstream-tenant-customer-production',
     'https://tenant.customer.example',
     'customer-production-01',
     1);

DECLARE @TenantDeploymentID int = CONVERT(int, SCOPE_IDENTITY());

INSERT dbo.tbl_TenantDeploymentKey
    (TDK_TenantDeploymentID, TDK_KeyID, TDK_PublicKeyPem, TDK_Enabled)
VALUES
    (@TenantDeploymentID,
     'tenant-customer-2026-01',
     N'-----BEGIN PUBLIC KEY-----
...contents of tenant-public.pem...
-----END PUBLIC KEY-----',
     1);

UPDATE dbo.tbl_Workspace
SET W_TenantDeploymentID = @TenantDeploymentID,
    W_ModifiedUTC = SYSUTCDATETIME()
WHERE WorkspaceID = 123;

COMMIT TRANSACTION;
```

Only the public key belongs in CoreDb. Never put `tenant-private.pem` in CoreDb.

The removal migration tries to bind legacy workspaces by their previous URL or hosting key. It stops without dropping columns if any workspace still has no deployment ID, allowing those workspaces to be assigned safely first.

New workspaces use the enabled deployment whose `TD_TenantApiBaseUrl` matches Core's `AppSettings:TenantApiBaseUrl`. Workspace creation fails safely if the default deployment is not registered.

---

## Core configuration

Core configuration contains its identity and private key only:

```json
{
  "InternalAuth": {
    "ServiceId": "matstream-core",
    "ExpectedAudience": "matstream-core",
    "KeyId": "core-2026-01",
    "PrivateKeyPath": "/run/secrets/core-private.pem",
    "SignatureLifetimeSeconds": 60,
    "AllowedClockSkewSeconds": 30,
    "ReplayRetentionSeconds": 180
  }
}
```

Core does not configure its own `PublicKeyPem` or `PublicKeyPath`. Its public key is distributed to Tenant deployments.

---

## Tenant configuration

A Tenant owns its private key and receives Core's public key:

```json
{
  "InternalAuth": {
    "ServiceId": "tenant-customer-production",
    "ExpectedAudience": "matstream-tenant-customer-production",
    "DefaultTargetAudience": "matstream-core",
    "KeyId": "tenant-customer-2026-01",
    "PrivateKeyPath": "/run/secrets/tenant-private.pem",
    "TrustedSigners": [
      {
        "ServiceId": "matstream-core",
        "KeyId": "core-2026-01",
        "PublicKeyPath": "/run/config/core-public.pem"
      }
    ]
  }
}
```

`PrivateKeyPem` and `PublicKeyPem` can be used instead of paths when a secrets manager injects PEM content. Environment variables use double underscores, for example `InternalAuth__PrivateKeyPath`.

MatStream uses a fully managed ECDSA implementation to parse and use these PEM keys. It does not import them into the Windows CNG key store, so IIS application pools do not need `Load User Profile` or cryptographic-profile permissions for internal API signing.

When a PEM is stored inline in JSON, line breaks must be written as `\n` with a backslash. `/n` with a forward slash corrupts the key and causes an `ASN1 corrupted data` error.

The key material is distributed as follows:

| Key file | Destination |
|---|---|
| `core-private.pem` | Core `InternalAuth:PrivateKeyPem` or `PrivateKeyPath` |
| `core-public.pem` | Each Tenant's Core entry under `TrustedSigners` |
| `tenant-private.pem` | That Tenant's `InternalAuth:PrivateKeyPem` or `PrivateKeyPath` |
| `tenant-public.pem` | CoreDb `tbl_TenantDeploymentKey.TDK_PublicKeyPem` |

Never place a private key in `tbl_TenantDeploymentKey`.

---

## Add another Tenant deployment

A second workspace on the same Tenant API continues to use the same deployment row and key pair. A separately hosted Tenant API needs:

1. A unique `TD_ServiceID`, `TD_Audience`, API base URL, and hosting key.
2. Its own ECDSA key pair and `TDK_KeyID`.
3. A new `tbl_TenantDeployment` row and public-key row.
4. `W_TenantDeploymentID` assigned to every workspace hosted by it.

Using the same value for `TD_ServiceID` and `TD_Audience` is the recommended naming convention. The Tenant's `InternalAuth:ServiceId` must match `TD_ServiceID`, and its `ExpectedAudience` must match `TD_Audience` exactly.

---

## Rotate a Tenant key

1. Generate a new Tenant key pair and choose a new `KeyId`.
2. Insert the new public key into `tbl_TenantDeploymentKey` for the existing deployment.
3. Change the Tenant's private key and `KeyId`.
4. Confirm signed requests work with the new key.
5. Disable and revoke the old database key row.

Core caches registry records for at most one minute. Keep both keys active during a planned transition, and account for the cache delay during emergency revocation. Never replace key material while reusing a `KeyId`.

Core key rotation works in the opposite direction: install the new Core public key on every Tenant first, then switch Core to the new private key and `KeyId`.

---

## Troubleshooting

### Internal endpoint returns 401

Check that:

- CoreDb contains an enabled, non-revoked Tenant key for the exact `ServiceId` and `KeyId`.
- The sender's private key matches the stored public key.
- The signed audience matches the receiver's `ExpectedAudience`.
- Both servers have synchronized clocks.
- A retry is not reusing an already accepted signature and nonce.

### Internal endpoint returns 403

Authentication succeeded, but the workspace is assigned to another deployment. Check `tbl_Workspace.W_TenantDeploymentID`.

### Core cannot sign an outbound Tenant request

Confirm that the target URL matches an enabled `TD_TenantApiBaseUrl` and that Core has `InternalAuth:PrivateKeyPath` or `InternalAuth:PrivateKeyPem`.

### Requests fail with multiple API instances

The default replay store is process-local. Replace `IInternalSignatureReplayStore` with shared storage such as Redis or a database so all instances see used nonces.

---

## Security rules

- Never send Core's private key to a Tenant.
- Never send a Tenant private key to Core; Core needs only its public key.
- Never commit private PEM files or inline private key configuration.
- Keep HTTPS enabled and server clocks synchronized.
- Revoke a key immediately if its private part may be exposed.
- Do not log private keys, full signature headers, or nonces.

---

## Related

- [API keys](../admin/api-keys.md) - user-managed credentials for external integrations
- [Coding agreements](coding-agreements.md) - MatStream development conventions
- [AI Assistant](../features/ai-assistant.md) - how MatStream AI uses this documentation
