# API contract — Reason of Wastage

Five existing endpoints (routes kept) and one new one. All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`. Shared responses (401, tenant middleware): [`project-overview.md` → API conventions](../../project-overview.md#api-conventions).

## Endpoints

| Endpoint | Stored procedure | Purpose | Change |
|---|---|---|---|
| `POST /Setting/GetWarehouseReason` | `SP_GetWarehouseReason` | List (this page, Wastage Entry) | Full list, sorted; paging ignored |
| `POST /Setting/GetWestageReasonById` | `SP_GetWestageReasonById` | Load one | Not deleted; `updatedOn` token; not-found message |
| `POST /Setting/AddEditResonMaster` | `SP_AddEditResonMaster` | Add (`0`) or rename | Permissions, validation, messages, stale check; `IsActive` ignored |
| `POST /Setting/ValidateWastageReason` | `SP_ValidateWastageReason` | Live text check | **New** |
| `POST /Setting/WestageReasonActive` | `SP_WestageReasonActive` | Activate / deactivate | Permission, not found, lock |
| `POST /Setting/DeleteWastageById` | none | Legacy delete | **Blocked** |

## Request / response shapes

### List reasons

**Request.** `{ "openMode": "All" | "Active", "searchText": "" }`; `pageNumber` / `pageSize` ignored.
**Response.** `Data`: array of `reasonMasterId`, `description`, `isActive`, `totalRecords` — active first, then A–Z.

### Get reason by id

**Request.** `{ "reasonMasterId": <int> }`. **Response.** `Data` with the same fields plus `updatedOn` (edit token, never null), sent back unchanged.

### Save reason

| Field | Rule |
|---|---|
| `reasonMasterId` | `0` = add; otherwise a non-deleted reason of the token's company |
| `description` | Required after trim; letters, numbers, spaces, `& - ( ) ' / . ,`; no max; unique per company (non-deleted) |
| `updatedOn` | **Edit only, required**: the value from get-by-id |
| `isActive`, `companyId`, `updatedBy` | Ignored (add is always Active; edit keeps status) |

**Response.** `Status=true`, `Data.reasonMasterId`; failures `Status=false` with a message below.

### Validate text

**Request.** `{ "description": "<string>", "reasonMasterId": <int> }`. **Response.** `Data.isExist = true` when used.

### Change status

**Request.** `{ "reasonMasterId": <int>, "isActive": <bool> }`. **Response.** `Status=true` (also when already in that state).

## Messages

| Endpoint | HTTP | `status` | Message (exact) | When |
|---|---|---|---|---|
| Save | 200 | false | `Reason is required` | Missing / empty / spaces |
| Save | 200 | false | `Reason can only contain letters, numbers, spaces and & - ( ) ' / . ,` | Other characters |
| Save | 200 | false | `Reason already exists` | `Exist` (today `Allready_Exit` with `true`) |
| Save, Get by id, Status | 200 | false | `Reason not found` | `NotFound` |
| Save (edit) | 200 | false | `This reason was changed by someone else. Reload and try again.` | `Stale` |
| Save, Status | 200 | false | `You don't have permission to perform this action.` | No `create_reason_of_wastage` (add) / `edit_reason_of_wastage` (rename, status) |
| Delete | 200 | false | `Reasons cannot be deleted. Deactivate the reason instead.` | Always |
| Any | 200 | false | `Something went wrong. Please try again.` | Unexpected error |
| Any | 401 | — | `{ "message": "Unauthorized" }` | No / invalid token, SuperAdmin token |

Success messages: add `Reason added`, rename `Reason updated`, status `Reason activated` / `Reason deactivated`.

## Auth and permissions

`[CompanyAuthorize]` on all six. Server permissions: add → `create_reason_of_wastage`; rename and status → `edit_reason_of_wastage`.
