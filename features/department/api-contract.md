# API contract — Department

Five existing endpoints and one new one. All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`. Shared responses (401, tenant middleware): [`project-overview.md` → API conventions](../../project-overview.md#api-conventions).

## Endpoints

| Endpoint | Stored procedure | Purpose | Change |
|---|---|---|---|
| `POST /AssetManagement/GetDepartmentList` | `SP_GetAllDepartment` | List (this page and Roster) | Full list, sorted; paging fields ignored |
| `POST /AssetManagement/GetDepartmentById` | `SP_GetDepartmentById` | Load one | Not deleted; `UpdatedAt`; not found message |
| `POST /AssetManagement/AddEditDepartment` | `SP_AddEditDepartment` | Add (empty id) or rename | Permissions, validation, messages, stale check; `IsActive` ignored |
| `POST /AssetManagement/ChangeDepartmentStatus` | `SP_ChangeDepartmentStatus` | Activate / deactivate | **New** |
| `POST /Users/ValidateDeptName` | `SP_ValidateDeptName` | Live name check | Trimmed, includes inactive |
| `POST /AssetManagement/DeleteDepartment` | none | Legacy delete | **Blocked** |
| `POST /Users/GetDepartmentDDl` | `SP_GetDepartmentDDl` | Active dropdown (User List) | Unchanged |

## Request / response shapes

### List departments

**Request.** Optional `{ "searchName": "" }`; `pageNumber` / `pageSize` ignored.
**Response.** `Data.departmentList`: `departmentMasterId`, `departmentName`, `companyId`, `isActive`, `createdAt`, `updatedAt`, `createdBy`, `updatedBy` — every non-deleted department of the company, active first then by name.

### Get department by id

**Request.** `{ "departmentMasterId": "<guid>" }`.
**Response.** `Data.department` with the same fields; `updatedAt` is the edit token (`UpdatedAt` or `CreatedAt` — never null), sent back unchanged.

### Save department

| Field | Rule |
|---|---|
| `departmentMasterId` | Empty GUID = add; otherwise a non-deleted department of the token's company |
| `departmentName` | Required after trim; ≤ 100; letters, numbers, spaces, `& - ( ) ' / . ,`; unique per company (non-deleted) |
| `updatedAt` | **Edit only, required**: the value from get-by-id |
| `isActive`, `companyId`, `createdBy`, `searchName`, `pageNumber`, `pageSize` | Ignored |

**Response.** `Status=true`, `Data.departmentMasterId`; on failure `Status=false` with a message below.

### Change status

**Request.** `{ "departmentMasterId": "<guid>", "isActive": <bool> }`.
**Response.** `Status=true` (also when already in that state).

### Validate name

**Request.** `{ "deptName": "<string>", "departmentMasterId": "<guid>" }`. **Response.** `Data.rtrValue = true` when used.

## Messages

| Endpoint | HTTP | `status` | Message (exact) | When |
|---|---|---|---|---|
| Save | 200 | false | `Department name is required` | Missing / empty / spaces |
| Save | 200 | false | `Department name must be 100 characters or less` | > 100 |
| Save | 200 | false | `Department name can only contain letters, numbers, spaces and & - ( ) ' / . ,` | Other characters |
| Save | 200 | false | `Department name already exists` | `Exist` |
| Save, Get by id, Change status | 200 | false | `Department not found` | `NotFound` |
| Save (edit) | 200 | false | `This department was changed by someone else. Reload and try again.` | `Stale` |
| Change status | 200 | false | `This department has active users. Move or deactivate them first.` | `HasActiveUsers` |
| Change status | 200 | false | `This department is used by active roster patterns. Deactivate them first.` | `HasActiveRosterPatterns` |
| Save, Change status | 200 | false | `You don't have permission to perform this action.` | No `create_department` (add) / `edit_department` (rename, status) |
| Delete | 200 | false | `Departments cannot be deleted. Deactivate the department instead.` | Always |
| Any | 200 | false | `Something went wrong. Please try again.` | Unexpected error |
| Any | 401 | — | `{ "message": "Unauthorized" }` | No / invalid token, SuperAdmin token |

## Auth and permissions

`[CompanyAuthorize]` everywhere above except `GetDepartmentDDl` (unchanged). Server permissions: add → `create_department`; rename and status → `edit_department`.
