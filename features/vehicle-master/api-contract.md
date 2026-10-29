# API contract — Vehicle Master

Four existing endpoints and one new endpoint. All `POST`, `{ Status, Message, Data }`, company and user from the JWT bearer token, `[CompanyAuthorize]`. Shared responses (401, tenant middleware): [`project-overview.md` → API conventions](../../project-overview.md#api-conventions).

## Endpoints

| Endpoint | Stored procedure | Purpose | Change |
|---|---|---|---|
| `POST /Setting/GetVechileList` | `SP_GetVechileList` | List vehicles for this company | Full list, company-scoped, sorted by `IsActive DESC, RegNo ASC` |
| `POST /Setting/GetVehicleById` | `SP_GetVehicleById` | Load single vehicle | Scoped by company; returns `LastUpdatedOn` edit token |
| `POST /Setting/AddEditVechile` | `SP_AddEditVehicle` | Add (`00000000-0000-0000-0000-000000000000`) or edit | Permissions, validation, exact messages, stale check; `CompanyId` from token |
| `POST /Setting/ChangeVehicleStatus` | `SP_ChangeVehicleStatus` | Activate / deactivate | **New** |
| `POST /Users/ValidateRegNo` | `SP_ValidateRegNo` | Live registration check | Trimmed compare, company-scoped, includes inactive |
| `POST /Setting/DeleteVehicle` | none | Legacy delete | **Blocked** (returns 200 with `Status=false`) |

## Request / response shapes

### List vehicles

**Request.** `{}` or `{ "activeOnly": true }` (dispatch: active vehicles only). Without a body the full list is returned, as today.
**Response.** `Data`: array of:
- `vehicleId` (string, GUID)
- `regNo` (string)
- `brandName` (string)
- `isActive` (boolean)
- `createdOn` (date string)
- `createdBy` (string, GUID)
- `lastUpdatedOn` (edit token; also returned by get-by-id)

### Get vehicle by id

**Request.** `{ "vehicleId": "<guid>" }`
**Response.** `Data` with vehicle fields, plus `lastUpdatedOn` (edit token, never null).

### Save vehicle

| Field | Rule |
|---|---|
| `vehicleId` | Empty GUID (`"00000000-0000-0000-0000-000000000000"`) = add; otherwise existing vehicle of token's company |
| `regNo` | Required after trim; 1–20 characters; letters and numbers only; uppercase; unique per company |
| `brandName` | Required after trim; 1–100 characters; letters, numbers, spaces, and `& - ( ) ' / . ,` |
| `lastUpdatedOn` | **Edit only, required**: the timestamp token from get-by-id |
| `isActive`, `companyId`, `createdBy`, `lastUpdatedBy` | Ignored from body; derived from state/token |

**Response.** `Status=true`, `Data.vehicleId`; on failure `Status=false` with exact message below.

### Change status

**Request.** `{ "vehicleId": "<guid>", "isActive": <bool> }`
**Response.** `Status=true` (idempotent; succeeds if already in that state).

### Validate registration number

**Request.** `{ "regNo": "<string>", "vehicleId": "<guid>" }`
**Response.** `Data.rtrValue = true` when the (trimmed, upper-cased) registration is used by another vehicle of the company, active or inactive.

## Messages

| Endpoint | HTTP | `status` | Message (exact) | When |
|---|---|---|---|---|
| Save | 200 | false | `Registration number is required` | Missing, empty, or whitespace-only |
| Save | 200 | false | `Registration number must be 20 characters or less` | > 20 characters |
| Save | 200 | false | `Registration number can only contain letters and numbers` | Contains spaces or special characters |
| Save | 200 | false | `Registration number already exists` | Duplicate in company (active or inactive) |
| Save | 200 | false | `Car manufacturer name is required` | Missing, empty, or whitespace-only |
| Save | 200 | false | `Car manufacturer name must be 100 characters or less` | > 100 characters |
| Save | 200 | false | `Car manufacturer name can only contain letters, numbers, spaces and & - ( ) ' / . ,` | Invalid characters |
| Save, Get by id, Status | 200 | false | `Vehicle not found` | Unknown GUID, deleted, or belongs to another company |
| Save (edit) | 200 | false | `This vehicle was changed by someone else. Reload and try again.` | Stale `lastUpdatedOn` token |
| Save, Status | 200 | false | `You don't have permission to perform this action.` | Missing `create_vehicle_master` (add) or `edit_vehicle_master` (edit/status) |
| Delete | 200 | false | `Vehicles cannot be deleted. Deactivate the vehicle instead.` | Always refused |
| Any | 200 | false | `Something went wrong. Please try again.` | Unexpected server exception |
| Any | 401 | — | `{ "message": "Unauthorized" }` | Missing, malformed, or SuperAdmin token |

Success messages: Add `Vehicle created successfully`, Edit `Vehicle updated successfully`, Status `Vehicle activated` / `Vehicle deactivated`.
