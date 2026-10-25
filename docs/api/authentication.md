# API authentication

MatStream integrations exchange API credentials for a bearer access token.

## Obtain credentials

Ask your workspace administrator to create an API key and provide:

- **Client ID**
- **Client secret**
- **Bound user email**
- **Workspace slug**

See [Managing API keys](../admin/api-keys.md). The email supplied when requesting a token must match the email bound to the key.

Keep the client secret in a secret store or environment variable. Run credential-based integrations on a trusted machine or server; do not embed secrets in a public webpage or browser application.

## Request a token

Send a JSON request:

```http
POST https://api.matstream.com/api/auth/api-token
Content-Type: application/json
```

```json
{
  "clientId": "YOUR_CLIENT_ID",
  "clientSecret": "YOUR_CLIENT_SECRET",
  "userEmail": "integration@example.com"
}
```

On success, read `data.token` and `data.expiration` from the response. The response envelope also contains `ok`.

## Use the token

Include this header on authenticated requests:

```http
Authorization: Bearer YOUR_ACCESS_TOKEN
```

The access token is currently issued for one hour. Use the returned expiration time rather than assuming a fixed lifetime. API key authentication does not issue a refresh token; request another access token with the same credentials when needed.

## Rotate or revoke credentials

Create replacement credentials, update your integration, verify its connection, and revoke the old key. Revocation prevents new token requests using that key. An already issued access token can remain usable until it expires.

Continue with [First API request](first-request.md) for a PowerShell example.
