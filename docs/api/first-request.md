# Your first API request

This example uses PowerShell to authenticate, locate a workspace, and search its entities. The requests read data; they do not modify entities.

## Before you start

You need API credentials, their bound user email, and a workspace slug. The user must have access to the workspace and permission to read entities.

Set the following environment variables for your integration before running the example:

- `MATSTREAM_CLIENT_ID`
- `MATSTREAM_CLIENT_SECRET`
- `MATSTREAM_USER_EMAIL`
- `MATSTREAM_WORKSPACE_SLUG`

Keep real credentials out of saved scripts, screenshots, and source control.

## 1. Authenticate

```powershell
$coreBase = 'https://api.matstream.com'
$credentials = @{
    clientId = $env:MATSTREAM_CLIENT_ID
    clientSecret = $env:MATSTREAM_CLIENT_SECRET
    userEmail = $env:MATSTREAM_USER_EMAIL
} | ConvertTo-Json

$auth = Invoke-RestMethod -Method Post `
    -Uri "$coreBase/api/auth/api-token" `
    -ContentType 'application/json' -Body $credentials

if (-not $auth.ok -or -not $auth.data.token) {
    throw 'Authentication did not return an access token.'
}
$headers = @{ Authorization = "Bearer $($auth.data.token)" }
```

## 2. Locate your workspace

```powershell
$slug = [Uri]::EscapeDataString($env:MATSTREAM_WORKSPACE_SLUG)
$resolved = Invoke-RestMethod -Method Get `
    -Uri "$coreBase/api/workspaces/resolve/$slug" -Headers $headers

if (-not $resolved.ok -or -not $resolved.data.tenantApiBaseUrl) {
    throw 'Workspace lookup did not return an API address.'
}
$workspaceId = $resolved.data.workspaceId
$workspaceBase = $resolved.data.tenantApiBaseUrl.TrimEnd('/')
```

Use the returned address for workspace requests. Do not hard-code an address copied from another workspace.

## 3. Search entities

```powershell
$search = @{
    query = ''
    pageNumber = 1
    pageSize = 10
    latestOnly = $true
} | ConvertTo-Json

$result = Invoke-RestMethod -Method Post `
    -Uri "$workspaceBase/api/wid/$workspaceId/entities/search" `
    -Headers $headers -ContentType 'application/json' -Body $search

if (-not $result.ok) {
    throw 'Entity search failed. Check the response error details.'
}
$result.data.rows
```

An empty query requests the first page of available entities. Set `query` to a search term to narrow the results. The response contains `data.total` and `data.rows`. Page numbers start at 1.

An empty result may simply mean there are no matching entities visible to this user. See [Errors and troubleshooting](errors.md) for failed requests.
