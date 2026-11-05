# API contract — Raw Material

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]` on every endpoint below.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetItemListPagination` | — | Adds `isActive`, `stockGroupId` filters; rows add `isActive`, `uom`, `purchaseCost`, `canDelete`, `totalCount` |
| `/Setting/GetStockItemById` | — | Company-scoped; `itemDetails` adds `isActive`, `updatedAt`, `isUsed`; "Raw material not found" |
| `/Setting/GetWarehouseItem` | — | Company-scoped |
| `/Setting/AddEditStockItem` | `create_raw_material` / `edit_raw_material` | Server validation; codes → messages; `status: false` on any refusal; `data.updatedAt`; `UpdatedAt` in the request (edit) |
| `/Setting/ChangeRawMaterialStatus` (**new**) | `edit_raw_material` | `{ stockItemId, isActive }` |
| `/Setting/DelStockItem` | `delete_raw_material` | Now delete-if-unused through `Sp_DeleteRawMaterial` |
| `/Setting/DeleteVendorById` | `edit_raw_material` | Company-scoped (vendor removal now happens in the save) |
| `/Setting/GetItemPriceHistory`, `GetUOM`, `GetStockGroup`, `GetVendorDDl`, `GetItemStock` | — | `[CompanyAuthorize]` |

## Messages (HTTP 200, `status: false`)

`Raw material name already exists ({type})` · `Category not found` · `Brand not found` · `Sub-brand not found` · `One or more locations are not available` · `One or more vendors are not available` · `The main unit can't change because this raw material is already in use.` · `This raw material is in use and can't be deleted. Deactivate it instead.` · `Raw material not found` · `This raw material was changed by someone else. Reload and try again.` · `You don't have permission to perform this action.` · `Something went wrong. Please try again.` · field messages. 401 for no / invalid / SuperAdmin token.

Success (UI): "Raw material created", "Raw material updated", "Raw material activated", "Raw material deactivated", "Raw material deleted".
