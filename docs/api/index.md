# MatStream API

Use the API to connect an ERP system, automate a workflow, or read MatStream data from your own software.

## How connections work

1. Obtain a client ID, client secret, and the email address bound to the API key from your workspace administrator.
2. Exchange those credentials for an access token at `https://api.matstream.com/api/auth/api-token`.
3. Resolve your workspace by its slug to obtain its workspace ID and API base address.
4. Send workspace requests to the returned address with your access token.

The workspace slug is the name used in the application's `/w/<slug>` address. The numeric workspace ID is returned by the API; do not substitute the slug where a numeric ID is required.

!!! note "Workspace addresses"
    Authentication and workspace lookup use `api.matstream.com`. Workspace data requests use the `tenantApiBaseUrl` returned by workspace lookup. Do not assume these are the same address.

## Start here

- [Authentication](authentication.md)
- [First API request](first-request.md)
- [Errors and troubleshooting](errors.md)
- [Managing API keys](../admin/api-keys.md)

## Permissions

An integration acts as the user associated with its credentials. That user must have workspace access and the permissions needed for each operation. An access token does not grant administrator permissions automatically.

## Endpoint reference

The guides here describe authentication, workspace lookup, and an entity search example. They are not a complete endpoint catalog.

For request and response schemas, open `/swagger/index.html` on the API deployment you are connecting to, where enabled. The Core API describes authentication and workspace lookup; the workspace API describes operations on workspace data. Only use endpoints intended for external integrations; internal service authentication is outside the scope of this guide.
