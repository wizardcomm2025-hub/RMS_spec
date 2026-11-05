# Data model — Raw Material

Tenant DB. **No table schema change.** Scripts: object files in `RMS_API/DBSCRIPT/SP/` (`CREATE OR ALTER`).

| Table | Use |
|---|---|
| `StockItem` | The raw material (`ClassificationId = 1`, `InvventoryTypeId` = the company's `Raw_Item` type). States: Active `IsActive = 1, IsDeleted = 0`; Inactive `IsActive = 0, IsDeleted = 0`; Deleted `IsDeleted = 1`. Edit token `ISNULL(UpdatedAt, CreatedAt)`. Cost / price `numeric(10,2)`, quantities `numeric(18,4)` |
| `ItemWarehouseMap` | Active locations of the item |
| `DistributorStockItemMapping` | Active vendors of the item; `IsDefault` is the per-vendor **Active** switch on the Vendors tab |
| `Stock`, `StockCheck` | A zero opening row per new location (as today) |
| `InventoryType`, `StockGroup`, `StockType`, `StockSubType`, `Warehouse`, `Distributor` | Read for company checks |

**"Used"** (Q1 delete, Q2 unit lock) — one definition in the new function `dbo.fn_IsStockItemUsed(@StockItemId)` (`DBSCRIPT/UDF/`) = any active row with the item in `ReceipeDetail`, `PurchaseOrderDetails`, `PoReceivedDetails`, `RequisitionDetail`, `RequisitionLineItem.ItemId`, `TransferDetail`, `Wastage`, `Production.ItemId`, `ProductionDetail.ItemId`, `Consumption`, or a `Stock` row with `Qty <> 0`. Zero opening rows don't count.

| Procedure | Change |
|---|---|
| `fn_IsStockItemUsed` (**new function**) | The "used" test above |
| `SP_GetItemListWithPagination` | New `@IsActive`, `@StockGroupId` filters; shows active **and** inactive, never deleted; returns `StockGroupId`, `IsActive`, `UOM`, `PurchaseCost`, `CanDelete` (not used) and `TotalCount`; without a valid page range returns every row |
| `SP_GetStockItemById` | New `@CompanyId` (company + not deleted); returns `IsActive`, `UpdatedAt` token, `IsUsed` (unit lock) |
| `SP_GetVendorListById`, `SP_GetWarehouseItem` | New optional `@CompanyId` (item must be the company's); Recipe keeps working |
| `SP_AddEditStockItem` | Rewritten: codes `ok` / `NotFound` / `Stale` / `Exist` (+ `ExistType`) / `BadCategory` / `BadBrand` / `BadSubBrand` / `BadParent` / `BadLocation` / `BadVendor` / `UomLocked`; `@VendorDetails` is now `vendorId#isDefault,…`; an unchanged category / brand / sub-brand / location / vendor is kept even if since switched off; an unused item's zero opening rows follow a unit change; `decimal` parameters; company-scoped update (never changes `CompanyId`, `IsActive`); vendor default per item; vendors removed when left out; opening stock for the item's own id; one transaction under `sp_getapplock` `StockItem_<CompanyId>`; returns the new edit token; `@ItemRemarks`, `@ItemNumber`, `@SKU` saved |
| `Sp_ChangeRawMaterialStatus` (**new**) | On / off, company-scoped |
| `Sp_DeleteRawMaterial` (**new**) | Delete if unused (`InUse` code otherwise); `SP_DelStockItem` stays for Recipe |
