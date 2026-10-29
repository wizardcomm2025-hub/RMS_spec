# API contract — Driver Master

Five existing endpoints (routes kept) and one new one. All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`. Shared responses (401, tenant middleware): [`project-overview.md` → API conventions](../../project-overview.md#api-conventions).

## Endpoints

| Endpoint | Stored procedure | Purpose | Change |
|---|---|---|---|
| `POST /Setting/GetDriverList` | `GetDriverList` | List (this page, Inbound Requisition dispatch) | Optional `activeOnly`; sorted; `lastUpdatedOn` |
| `POST /Setting/GetDriverById` | `GetDriverList` (one) | Load one | "Driver not found"; `lastUpdatedOn` |
| `POST /Setting/AddEditDriver` | `SP_AddEditDriver` | Add (empty id) or edit | Permissions, validation, photo check, messages, stale check; `IsActive` ignored; `Status=false` on any refusal |
| `POST /Setting/ChangeDriverStatus` | `SP_ChangeDriverStatus` | Activate / deactivate | **New** |
| `POST /Users/ValidateLicNo` | `SP_ValidateLicNo` | Live licence check | **Was anonymous**: now `[CompanyAuthorize]`, company from the token (`CompanyId` in the body ignored); includes inactive |
| `POST /Setting/DeleteDriver` | none | Legacy delete | **Blocked** |

## Request / response shapes

### List drivers

**Request.** `{}` or `{ "activeOnly": true }`. **Response.** `Data`: array of `driverId`, `driverName`, `licNo`, `profileImage`, `createdOn`, `isActive`, `lastUpdatedOn` — active first, then by name.

### Get driver by id

**Request.** `{ "driverId": "<guid>" }`. **Response.** `Data` with the same fields; `lastUpdatedOn` is the edit token (never null), sent back unchanged.

### Save driver

| Field | Rule |
|---|---|
| `driverId` | Empty GUID = add; otherwise a driver of the token's company |
| `driverName` | Required after trim; ≤ 100; letters, numbers, spaces, `& - ( ) ' / . ,` |
| `licNo` | Required after trim; ≤ 50; letters, numbers, spaces, hyphens; stored uppercase; unique per company |
| `profileImage` | `null` = keep the current photo (edit) / none (add); `""` = remove; otherwise a `data:image/jpeg;base64,…` or `data:image/png;base64,…` URL of ≤ 1 MB decoded |
| `lastUpdatedOn` | **Edit only, required**: the value from get-by-id |
| `isActive`, `companyId` | Ignored |

**Response.** `Status=true`, `Data.driverId`; failures `Status=false` with a message below.

### Change status

**Request.** `{ "driverId": "<guid>", "isActive": <bool> }`. **Response.** `Status=true` (also when already in that state).

### Validate licence

**Request.** `{ "licNo": "<string>", "driverId": "<guid>" }`. **Response.** `Data.rtrValue = true` when another driver of the company uses it (trimmed, case-insensitive, active or inactive).

## Messages

| Endpoint | HTTP | `status` | Message (exact) | When |
|---|---|---|---|---|
| Save | 200 | false | `Driver name is required` | Missing / empty |
| Save | 200 | false | `Driver name must be 100 characters or less` | > 100 |
| Save | 200 | false | `Driver name can only contain letters, numbers, spaces and & - ( ) ' / . ,` | Other characters |
| Save | 200 | false | `License number is required` | Missing / empty |
| Save | 200 | false | `License number must be 50 characters or less` | > 50 |
| Save | 200 | false | `License number can only contain letters, numbers, hyphens and spaces` | Other characters |
| Save | 200 | false | `License number already exists` | `Exist` |
| Save | 200 | false | `Profile picture must be a JPG or PNG image of 1 MB or less` | Wrong type, not a data URL, or too big |
| Save, Get by id, Status | 200 | false | `Driver not found` | `NotFound` (unknown or another company's id) |
| Save (edit) | 200 | false | `This driver was changed by someone else. Reload and try again.` | `Stale` |
| Save, Status | 200 | false | `You don't have permission to perform this action.` | No `create_driver_master` (add) / `edit_driver_master` (edit, status) |
| Delete | 200 | false | `Drivers cannot be deleted. Deactivate the driver instead.` | Always |
| Any | 200 | false | `Something went wrong. Please try again.` | Unexpected error |
| Any | 401 | — | `{ "message": "Unauthorized" }` | No / invalid token, SuperAdmin token |

Success messages (UI): `Driver created successfully`, `Driver updated successfully`, `Driver activated`, `Driver deactivated`.

## Auth and permissions

`[CompanyAuthorize]` on all six. Server permissions: add → `create_driver_master`; edit and status → `edit_driver_master`.
