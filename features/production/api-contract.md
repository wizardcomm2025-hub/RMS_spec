# API contract — Production

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetProductionList` (**new**) | `viewlist_product` | `{ pageNumber, pageSize, fromDate, toDate, locationId, search }` → rows (`productionId, productionDate, itemName, qty, uom, locationName, totalStockCost, totalAdjustmentCost, overheadCost, finalProductionCost, unitCost, createdByName, createdAt`) + `totalCount` |
| `/Setting/GetProductionItems` (**new**) | `create_product` | `{ search }` → `stockItemId, itemName, uom, batchQty, yieldPercent, ingredientCount` |
| `/Setting/GetProductionSheet` (**new**) | `create_product` | `{ itemId, locationId }` → header (`itemName, uom, batchQty, yieldPercent`) + lines (`stockItemId, itemName, uom, perBatchQty, stockQty, unitCost`) |
| `/Setting/SaveProduction` (**new**) | `create_product` | `{ itemId, locationId, productionDate (yyyy-MM-dd), qty, overheadCost, lines: [{ stockItemId, actualQty, adjustmentCost, adjustmentReason }] }` → `productionId` |
| `/Setting/GetProductionDetail` (**new**) | `viewdetails_product` | `{ productionId }` → header (`productionId, itemName, qty, uom, locationName, productionDate, createdByName, createdAt, totalStockCost, totalAdjustmentCost, overheadCost, finalProductionCost, unitCost, companyName`) + lines (`itemName, uom, standardQty, actualQty, unitCost, itemCost, adjustmentCost, adjustmentReason`) |
| `/Setting/AddEditProduction` | `create_product` | Kept; new production only, mapped to `SaveProduction` with Actual = standard; `ProductionId <> 0` → "Productions can't be changed after saving." |
| `/Setting/GetProductionDt`, `GetProductionDetailById` | `viewlist_product` / `viewdetails_product` | Kept; `[CompanyAuthorize]` |
| `/Setting/ItemSuggestion`, `GetRecipeItemDetails_IncludingProduction` | `create_product` | Kept; `[CompanyAuthorize]` |

Messages (`status: false`): as in [02](./02-record-production.md). 401 for no / invalid / SuperAdmin token. Success: "Production of {qty} {uom} {item} saved".
