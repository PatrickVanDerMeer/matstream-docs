# API errors and troubleshooting

Start with the HTTP status code. When a response contains a MatStream error envelope, inspect `error.message`, `error.code`, and `error.status`. Some authentication, validation, or infrastructure failures can return another format or an empty body, so do not assume every error has the same JSON shape.

| HTTP status | What to check |
| --- | --- |
| 400 | Required fields, JSON types, route IDs, and request validation details. |
| 401 | Credentials, bound user email, token expiration, and the `Authorization: Bearer ...` header. |
| 403 | The user's workspace access and grants for the operation. |
| 404 | Endpoint spelling, workspace slug, resource ID, and the workspace API address returned by lookup. A workspace lookup can also return 404 when the workspace is unavailable to the user. |
| 429 | Request frequency. Honor `Retry-After` if present and reduce concurrent requests. |
| 500-599 | Service availability. Record the time and request details for support. |

## Authentication fails

Confirm that the key is enabled and has not expired or been revoked. Supply the bound user email along with the client ID and secret. Never send the client secret as the bearer token.

## Authentication works but workspace requests fail

Resolve the workspace again and use the returned `workspaceId` and `tenantApiBaseUrl`. Check that the associated user can access that workspace and perform the requested action.

## Handle responses

Successful responses generally use an envelope with `ok: true` and a `data` value. Application errors can use `ok: false` and an `error` object. Check both the HTTP status and the response body.

Only retry writes when you know the operation can safely be repeated. A connection failure does not prove that the server failed to apply a change.

## Contact support

Include the request method, endpoint path, HTTP status, time of failure, and a trace ID if returned. Remove authorization headers, tokens, client secrets, and sensitive request data before sharing logs.
