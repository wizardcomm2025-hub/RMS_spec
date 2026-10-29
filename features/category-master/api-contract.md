# API contract — Category Master

The screen uses eight endpoints in `SettingController` and `UsersController` and adds one (`ChangeStockGroupStatus`). All are `POST`, return `{ Status, Message, Data }`, take `CompanyId` and `UserId` from the token, and use `[CompanyAuthorize]` (company users only). Endpoints shared with other screens change additively only.

> Shared responses (401 from the auth attributes, tenant-middleware 404 / 403 / 401) are in [`project-overview.md` → API conventions](../../project-overview.md#api-conventions).

## Endpoints

| Endpoint | Auth | Stored procedure | Purpose | Change |
|---|---|---|---|---|
| `POST /Setting/GetStockGroupList` | `[CompanyAuthorize]` | `SP_GetStockGroupList` | List item categories | Include inactive; add `IsActive` |
| `POST /Setting/GetStockGroupById` | `[CompanyAuthorize]` | `SP_GetStockGroupById` | Load one | Company filter; not found; add `IsActive`, `UpdatedAt`, `InventoryTypeName` |
| `POST /Setting/AddEditStockGroup` | `[CompanyAuthorize]` | `SP_AddEditStockGroup` | Add (empty id) or edit | Permissions, validation, codes → messages, stale check, company filter |
| `POST /Setting/ChangeStockGroupStatus` | `[CompanyAuthorize]` | `SP_ChangeStockGroupStatus` | Activate / deactivate | **New** |
| `POST /Users/ValidateStockGroupName` | `[CompanyAuthorize]` | `SP_ValidateStockGroupName` | Live name check | Trim; per company + type (`InventoryTypeId`, optional) |
| `POST /Setting/GetInventoryTypeCompanyWise` | `[CompanyAuthorize]` | `SP_GetInventoryTypeCompanyWise` | Inventory types | Generic error only (shared) |
| `POST /Setting/GetStockGroupByInventoryType` | `[CompanyAuthorize]` | `SP_GetStockGroupByInventoryType` | Parent list | Generic error only (shared with Recipe) |
| `POST /Setting/DelStockGroupById` | `[CompanyAuthorize]` | none | Legacy delete | **Blocked**: always refuses |

## Request / response shapes

### List categories

**Request.** No body.
**Response.** `Data`: array — `StockGroupId`, `StockGroupName`, `StockGroupAlias`, `InventoryTypeId`, `InventoryTypeName`, `ParentStockGroupId` (empty GUID for a main category), `ClassificationId` (always 1), **`IsActive`** (new), and the existing `ThirdPartyId`, `CompanyId`, `ImagePath`, `IsIntangible`. Only the token company's non-deleted item categories.

### Get category by id

**Request.** `{ "StockGroupId": "<guid>" }`.
**Response.** Same fields as the list plus **`UpdatedAt`** — the edit token (`UpdatedAt`, or `CreatedAt` when never updated; never null). The UI sends it back unchanged, as a string.

### Save category

| Field | Type | Rule |
|---|---|---|
| `StockGroupId` | guid | Empty GUID = add; otherwise a non-deleted item category of the token's company |
| `StockGroupName` | string | Required after trim; ≤ 100; letters, numbers, spaces, `& - ( ) ' / . ,`; unique per company + type |
| `StockGroupAlias` | string | Category Code. Required after trim; 1–10 letters/digits; stored upper-case; unique per company + type |
| `InventoryTypeId` | long | An active inventory type of the company |
| `ParentStockGroupId` | guid | Empty GUID = main category; else an active main item category of the same company and type, not itself |
| `UpdatedAt` | datetime | **Edit only, required**: the value from get-by-id; else refused as stale |
| `ClasificationId` | — | Ignored; always 1 |
| `CompanyId`, `CreatedBy`, `ThirdPartyId`, `ParentId`, `ImagePath`, `IsIntangible`, `IsActive`, `IsDeleted` | — | Ignored; insert uses today's defaults, edit keeps stored values |

**Response.** `Status=true`, `Data.Response = "ok"`-style result; on failure `Status=false` with a message below.

### Change status

**Request.** `{ "stockGroupId": "<guid>", "isActive": <bool> }`.
**Response.** `Status=true` on success, also when already in that state.

### Validate name

**Request.** `{ "stkGroupName": "<string>", "stkGroupId": "<guid>", "classificationId": 1, "inventoryTypeId": <long> }`.
**Response.** `Data.rtrValue = true` when the name is already used.

## Messages

| Endpoint | HTTP | `status` | Message (exact) | When |
|---|---|---|---|---|
| Save | 200 | false | `Category name is required` | Missing / empty / spaces |
| Save | 200 | false | `Category name must be 100 characters or less` | > 100 after trim |
| Save | 200 | false | `Category name can only contain letters, numbers, spaces and & - ( ) ' / . ,` | Other characters |
| Save | 200 | false | `Category name already exists` | `Exist` |
| Save | 200 | false | `Category code is required` | Missing / empty |
| Save | 200 | false | `Category code must be 1–10 letters or digits` | Bad format |
| Save | 200 | false | `Category code already exists` | `CodeExist` |
| Save | 200 | false | `Inventory type is required` | Missing, or `InvalidType` |
| Save | 200 | false | `Select a valid parent category` | `InvalidParent` |
| Save | 200 | false | `A category with sub-categories can't be made a sub-category` | `HasChildren` |
| Save | 200 | false | `Inventory type can't be changed while the category has sub-categories or items` | `TypeLocked` |
| Save, Get by id, Change status | 200 | false | `Category not found` | `NotFound` |
| Save (edit) | 200 | false | `This category was changed by someone else. Reload and try again.` | `Stale` |
| Change status | 200 | false | `Deactivate its sub-categories first.` | `HasActiveChildren` |
| Change status | 200 | false | `This category is used by active items. Move or deactivate them first.` | `UsedByItems` |
| Change status | 200 | false | `Activate the parent category first.` | `ParentInactive` |
| Save, Change status | 200 | false | `You don't have permission to perform this action.` | No `create_item_category` (add) / `edit_item_category` (edit, status) |
| Delete (legacy) | 200 | false | `Categories cannot be deleted. Deactivate the category instead.` | Always |
| Any | 200 | false | `Something went wrong. Please try again.` | Unexpected error; detail only in the log |
| Any | 401 | — | `{ "message": "Unauthorized" }` | No / invalid token, or a SuperAdmin token |

## Auth and permissions

- `[CompanyAuthorize]` on every endpoint above (SuperAdmin tokens rejected).
- Server-side permissions: add → `create_item_category`; edit and status → `edit_item_category`. Reads need only a valid company token.
- `CompanyId`, `UserId` and `ClassificationId` are never taken from the body.
