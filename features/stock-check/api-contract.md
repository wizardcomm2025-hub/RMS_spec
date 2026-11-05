# API contract — Stock Check

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetStockCheckSheet` (**new**) | `viewlist_stock_check` | `{ warehouseId, inventoryTypeId }` → rows (`stockItemId, stockItemName, stockGroupName, mainUom, altUom, conversion, systemQty, lastCountQty, lastCountAt, lastCountBy`) |
| `/Setting/SaveStockCounts` (**new**) | `create_stock_check` | `{ warehouseId, lines: [{ stockItemId, qty, uom, note }] }` (max 1000 lines, note ≤ 500) → `{ saved }` |
| `/Setting/AddEditStockCheck`, `/Setting/SaveStockCheckIndv` | `create_stock_check` | Kept for other callers; mapped to `SaveStockCounts` (rows with `qty` ≤ 0 in the old bulk payload are **skipped**, since that payload has no "not counted") |
| `/Setting/GetStockCheckItemListMarge` | `viewlist_stock_check` | Kept; `[CompanyAuthorize]` |

Messages (`status: false`): `Select a location` · `Enter a count for at least one item` · `Counts can't be negative` · `{item} can't be counted in {uom}` · `One or more items aren't available at this location` · `You don't have permission to perform this action.` · `Something went wrong. Please try again.`. 401 for no / invalid / SuperAdmin token.

Success: "{n} counts saved" / "{item} count saved".
