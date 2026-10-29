# API contract — {{FEATURE_NAME}}

<!-- TODO: one paragraph describing the API surface this feature exposes. -->

> Until `api/openapi.yaml` is in use (see `GETTING-STARTED.md`), **this file is the contract**. Every RMS endpoint is `POST /{Controller}/{Method}` and returns `{ Status, Message, Data }`. Responses produced by shared layers (401 from the auth attributes, tenant-middleware errors) are listed once in [`project-overview.md`](../../project-overview.md#api-conventions); link them, don't repeat them.

## Endpoints

<!-- TODO: list each endpoint this feature calls or owns: path, auth attribute, stored procedure, one-line purpose. -->

| Endpoint | Auth | Stored procedure | Purpose |
|---|---|---|---|
| `POST /<Controller>/<Method>` | `[Authorize]` | `<SP name>` | <one line> |

## Request / response shapes

<!-- TODO: per endpoint, the request fields (name, type, required, rule) and the response Data fields. Mark fields taken from the token. -->

### `POST /<Controller>/<Method>`

**Request.** <note on params, headers, body if any.>

**Response.** <fields of `Data`, and which fields are new (additive) vs existing.>

## Errors

<!-- TODO: per endpoint or globally, what error responses are possible and what each means. -->

| HTTP | `status` | Message (exact) | When |
|---|---|---|---|
| 200 | false | `<exact text>` | <validation / duplicate / not found> |
| 401 | — | see API conventions | no or invalid token |

## Auth and permissions

<!-- TODO: auth attribute per endpoint; whether the server enforces the menu permission (e.g. `<Module>.Create`) or only the UI does; which fields are always taken from the token (CompanyId, UserId). -->
