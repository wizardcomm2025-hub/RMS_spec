# API contract — Brand and Sub Brand

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]` everywhere below. Shared responses: [`project-overview.md` → API conventions](../../project-overview.md#api-conventions).

## Endpoints

| Endpoint | Procedure | Change |
|---|---|---|
| `GetStockTypeWithPagination` | `SP_GetStockTypeWithPagination` | Full list + counts + `updatedAt` |
| `GetStockTypeDetailsById` | `SP_GetStockTypeDetailsById` | Company-scoped; "Brand not found" |
| `AddEditStockDetailsType` | `SP_AddEditStockTypeDetails` | Permissions, validation, codes → messages, stale check |
| `ChangeStockTypeStatus` | `SP_ChangeStockTypeStatus` | **New** |
| `GetAllStockTypeDetails` | `SP_GetAllStockType` | Active brands (Sub Brand dialog); unchanged |
| `DeleteStockType` | — | **Refused** |
| `GetStockSubTypeWithPagination` | `SP_GetStockSubTypeWithPagination` | All sub-brands + brand + count + `updatedAt` |
| `GetStockSubTypeDetailsById` | `SP_GetStockSubTypeDetailsById` | Company-scoped, any state |
| `AddEditStockSubDetailsType` | `SP_AddEditStockSubTypeDetails` | As brand + brand / move rules |
| `ChangeStockSubTypeStatus` | `SP_ChangeStockSubTypeStatus` | **New** |
| `DeleteStockSubType` | — | **Refused** |
| `/Users/ValidateStockTypeName`, `/Users/ValidateStockSubTypeName` | validate SPs | `[CompanyAuthorize]` |
| `GetStockTypeDDl`, `GetStockSubTypeDDl`, `GetStockTypeList`, `GetStockSubTypeList` | unchanged | `[CompanyAuthorize]` |
| **Legacy** `AddEditStockType`, `AddEditStockSubType`, `GetStockTypeById`, `GetStockSubTypeById` | — | **Refused** (were anonymous, D1): `This endpoint is no longer available.` |

(Routes are under `/Setting/` unless shown.)

## Save fields

Brand: `stockTypeId` (empty GUID = add), `stockTypeName`, `updatedAt` (edit, required); `isActive`, `isDeleted`, `isShortShelfLife`, `companyId` ignored.
Sub-brand: `stockSubTypeId`, `stockTypeId` (required, active brand of the company), `stockSubTypeName`, `updatedAt`; `isActive`, `isDeleted`, `companyId` ignored.
Status: `{ "stockTypeId": <guid>, "isActive": <bool> }` / `{ "stockSubTypeId": <guid>, "isActive": <bool> }`.

## Messages (all HTTP 200, `status: false`)

| When | Brand | Sub-brand |
|---|---|---|
| Name missing / > 100 / characters / duplicate | `Brand name is required` · `Brand name must be 100 characters or less` · `Brand name can only contain letters, numbers, spaces and & - ( ) ' / . ,` · `Brand name already exists` | `Sub-brand name is required` · `Sub-brand name must be 100 characters or less` · `Sub-brand name can only contain letters, numbers, spaces and & - ( ) ' / . ,` · `Sub-brand name already exists under this brand` |
| Brand missing / not found / inactive | — | `Brand is required` · `Brand not found` · `Pick an active brand.` |
| Not found | `Brand not found` | `Sub-brand not found` |
| Stale | `This brand was changed by someone else. Reload and try again.` | `This sub-brand was changed by someone else. Reload and try again.` |
| Move while used | — | `This sub-brand is used by active raw materials, so its brand can't be changed.` |
| Switch off refused | `This brand has active sub-brands. Switch them off first.` · `This brand is used by active raw materials. Move them first.` | `This sub-brand is used by active raw materials. Move them first.` |
| Switch on refused | — | `Switch its brand on first.` |
| Delete | `Brands cannot be deleted. Deactivate the brand instead.` | `Sub-brands cannot be deleted. Deactivate the sub-brand instead.` |
| Permission | `You don't have permission to perform this action.` | same |
| Error | `Something went wrong. Please try again.` | same |

401 `{ "message": "Unauthorized" }` for no / invalid / SuperAdmin token. Success (UI): "Brand created / updated / activated / deactivated", "Sub-brand created / updated / activated / deactivated".
