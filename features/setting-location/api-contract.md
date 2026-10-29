# API contract — Setting → Location

The Location screen uses six endpoints in `SettingController`, `UsersController` and `ProductController`, and adds one (`ChangeWareHouseStatus`). All are `POST`, return `{ Status, Message, Data }`, and take `CompanyId` and `UserId` from the token. Existing contracts change **additively** only, because other screens call them.

> Shared responses (401 from `[Authorize]`, tenant-middleware 404 / 403 / 401) are listed in [`project-overview.md` → API conventions](../../project-overview.md#api-conventions) and are not repeated here.

## Endpoints

| Endpoint | Auth | Stored procedure | Purpose | Change |
|---|---|---|---|---|
| `POST /Setting/GetWareHouseDetails` | `[Authorize]` | `Sp_GetWareHouseandBinDetails` | List locations | Add `IsActive`; optional `IncludeInactive` (LOC-Q5) |
| `POST /Setting/GetWareHouseDetailsById` | **`[Authorize]`** (was commented out) | `GetWarehouseBinDetailsbyWarehouseId`, `SelectedEmployeeByWarehouse` | Load one location | Auth; not found; add `IsActive`, `UpdatedAt` |
| `POST /Setting/SaveWareHouseDetails` | `[Authorize]` | `SP_ValidateLocName`, `Sp_SaveWareHouseDetails` | Add (`WarehouseId = 0`) or edit | Permission, validation, stale check, `Status=false` on failure |
| `POST /Setting/ChangeWareHouseStatus` | `[Authorize]` | `Sp_ChangeWareHouseStatus` | Activate / deactivate | **New**; permission check |
| `POST /Setting/GetAllLocationDDl` | `[Authorize]` | `SP_GetAllMap_Company_LocationType` | Company's location types | Standard failure response |
| `POST /Users/ValidateLocName` | `[Authorize]` | `SP_ValidateLocName` | Live duplicate check | Trimmed, includes inactive |
| `POST /Product/GetUserWarehouse` | `[Authorize]` (already in code; verified 2026-10-28) + company user only | `SP_GetUserWarehouse` | Employees for the picker | Reject SuperAdmin token (LOC-Q11) |
| `POST /Setting/DeleteWareHouseDetails` | `[Authorize]` | none (no longer calls `DeleteWareHouseAndBin`) | Legacy delete, not called by the UI | **Blocked**: always refuses (LOC-Q8) |

## Request / response shapes

### List locations

**Request.** Optional body `{ "IncludeInactive": <bool> }`, default `false`. No body = today's behaviour.
**Response.** `Data`: array of locations — `WarehouseId`, `WarehouseName`, `WarehouseType`, address fields, `IsDefault`, `CanTakeRequisition`, `IsPOSOutlet`, **`IsActive`** (new). Only the token company's rows. `IncludeInactive: true` (Location screen only) → all `IsDeleted = 0` rows, active first then name; `false` / absent (the 8 other callers: Order Channel ×2, PO Receive, Recipe, Yield Report, Stock Item, User form, Yield Entry) → active rows only (LOC-Q5).

### Get location by id

**Request.** `{ "WarehouseId": <long> }`. `CompanyId` and `UserId` in the body are ignored.
**Response.** `Data`: `WarehouseId`, `WarehouseName`, `WarehouseType`, `Address` (UI line 1), `AddressLine1` (UI line 2), `City`, `StateName`, `Zip`, `IsDefault`, `CanTakeRequisition`, `IsPOSOutlet`, **`IsActive`**, **`UpdatedAt`** (edit token: the row's `UpdatedAt`, or `CreatedAt` when never updated — never null; send it back unchanged, as an opaque string), `UserResponsible`, `SelectedEmployee`, `SelectedEmployeByWarehouse`, `Bin[]`.

### Save location

**Request.**

| Field | Type | Rule |
|---|---|---|
| `WarehouseId` | long | `0` = add; otherwise an existing, non-deleted location of the token's company |
| `WarehouseName` | string | Required after trim; ≤ 100; unique per company among non-deleted |
| `WarehouseType` | string | Required; one of the company's active types, else "Location Type is required" |
| `AddressLine1`, `AddressLine`, `City`, `StateName` | string? | Optional; ≤ 500 |
| `Zip` | string? | Empty or exactly 6 digits |
| `IsDefault` | bool | See [05](./05-default-location.md); `true` on an inactive location is refused |
| `CanTakeRequisition` | bool | Stored as sent |
| `IsPOSOutlet` | bool | Ignored; always stored as `true` |
| `EmployeeDetails[]` | `{ SelectedUserId }` | Newly added ids must be active, non-customer users of the company; ids already mapped to this location are accepted (LOC-Q6) |
| `UpdatedAt` | datetime | **Edit only, required.** The value from get-by-id, unchanged; must equal the stored `ISNULL(UpdatedAt, CreatedAt)`, else refused as stale (LOC-Q7). Missing or null on an edit → stale. Ignored on add |
| `BinNumbers[]` | — | Not sent by the UI; must not change bins |
| `ThirdPartyId`, `ContactNumber1`, `ContactNumber2` | string? | Not on the form; stored as sent |
| `CompanyId`, `UserId`, `CreatedBy`, `UserResponsible`, `IsActive`, `IsDeleted` | — | **Not part of the request.** Ignored if sent |

**Response.** `Status=true`, `Data.Response` = `'OK'`-style result. On failure `Status=false` with a message below.

### Change status

**Request.** `{ "warehouseId": <long>, "isActive": <bool> }`. No `UpdatedAt` check (a switch always applies), but it sets `UpdatedAt`.
**Response.** `Response<ResponseChangeWarehouseStatus>`; `Status=true` on success, also when the location already has that state.

### Validate name

**Request.** `{ "LocName": <string>, "locationId": <long|null> }`.
**Response.** `Data.rtrValue` (boolean the UI uses to show the duplicate error).

## Messages

Exact text. All decided 2026-10-28 (LOC-Q1 – Q11).

| Endpoint | HTTP | `status` | Message | When |
|---|---|---|---|---|
| Save | 200 | false | `Location name already exists` | Duplicate among the company's non-deleted locations |
| Save | 200 | false | `Location Name is required` | Missing, empty or spaces only |
| Save | 200 | false | `Location Name must be 100 characters or less` | > 100 after trim |
| Save | 200 | false | `Pincode must be 6 digits` | `Zip` not empty and not exactly 6 digits |
| Save | 200 | false | `Location Type is required` | Missing, or not one of the company's active types (LOC-Q10) |
| Save | 200 | false | `One or more employees are not valid.` | A newly added employee is not active, non-customer staff of the company (LOC-Q6) |
| Save | 200 | false | `Activate the location to make it default` | `IsDefault = true` on an inactive location (LOC-Q3) |
| Save | 200 | false | `Location not found` | `WarehouseId` unknown, deleted or another company's (LOC-Q9) |
| Save (edit) | 200 | false | `This location was changed by someone else. Reload and try again.` | `UpdatedAt` missing or different from the stored value (LOC-Q7) |
| Save, Change status | 200 | false | `You don't have permission to perform this action.` | Caller lacks `Location.Create` (add) or `Location.Edit` (edit, status) (LOC-Q1) |
| Get by id, Change status | 200 | false | `Location not found` | Unknown, deleted or another company's id |
| Delete (legacy) | 200 | false | `Locations cannot be deleted. Deactivate the location instead.` | Always; nothing written (LOC-Q8) |
| Any | 200 | false | `Something went wrong. Please try again.` | Unexpected error; detail only in the log (LOC-Q2) |
| Any `[Authorize]` | 401 | — | `{ "message": "Unauthorized" }` | No / invalid / expired token, or a SuperAdmin token (LOC-Q11) |

Validation order when several rules fail: permission → not found → stale → field rules (name, type, pincode, employees, default) → duplicate. Tests that break one rule at a time are not affected by the order.

## Auth and permissions

- Every endpoint here requires a valid company token, including `GetUserWarehouse` (LOC-Q4). A SuperAdmin token is rejected with 401 (LOC-Q11).
- The API enforces menu permissions (LOC-Q1): add needs `Location.Create`; edit and status change need `Location.Edit`. Reads (list, get by id, types, validate name, employees) need only a valid token.
- `CompanyId` and `UserId` always come from the token; the same fields in a body are ignored.
- Unexpected errors return the generic message; exception text goes only to the log (LOC-Q2).
