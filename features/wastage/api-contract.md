# API contract — Wastage

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetWastageSheet` (**new**) | `viewlist_wastage` | `{ warehouseId, inventoryTypeId }` → rows (`stockItemId, stockItemName, stockGroupName, mainUom, altUom, conversion, stockQty`) |
| `/Setting/FindWastageBarcode` (**new**) | `viewlist_wastage` | `{ warehouseId, barcode }` → `barcodeId, barcodeNumber, stockItemId, remainingQty, uom` or "Barcode not found at this location" |
| `/Setting/SaveWastage` (**new**) | `create_wastage` | `{ warehouseId, lines: [{ stockItemId, qty, uom, reasonId, barcodeId? }] }` (max 500 lines) → `{ saved }` |
| `/Setting/GetWastageToday` (**new**) | `viewlist_wastage` | `{ warehouseId }` → `wastageId, createdAt, stockItemName, qty, uom, reason, barcodeNumber, createdBy` |
| `/Setting/CancelWastage` (**new**) | `delete_wastage` | `{ wastageId }` |
| `/Setting/AddEditWastage` | `create_wastage` | Kept; mapped to `SaveWastage` |
| `/Setting/GetStockWastage`, `/Setting/SearchWastageByBarCode`, `/Setting/GetStockGroupWastage` | `viewlist_wastage` | Kept; `[CompanyAuthorize]` |

Messages (`status: false`): as in [02](./02-record-wastage.md) and [03](./03-cancel-today.md), plus `Something went wrong. Please try again.`. 401 for no / invalid / SuperAdmin token.

Success: "{n} wastage entries saved" / "{item} wastage saved" / "Wastage entry cancelled".
