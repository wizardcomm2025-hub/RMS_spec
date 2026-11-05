# Data model — Vendor

Tenant DB. **No table schema change.** Scripts in `RMS_API/DBSCRIPT/` (`CREATE OR ALTER`).

| Table | Use |
|---|---|
| `Distributor` | The vendor: `DistributorName`, `GSTNo`, `Website`, `IsActive`, `IsDelete`, `ForInventory`, `ForAsset`, `AcceptDispose`, `CompanyId`. Edit token `ISNULL(UpdatedAt, CreatedAt)` |
| `DistributorContact` | Contact rows; the vendor's **primary** = `IsPrimary = 1`, `IsActive = 1` (else the oldest active row) |
| `DistributorStockItemMapping`, `PurchaseOrderMaster.VendorId`, `ReceiveHead.VendorId`, `Barcode.DistributorId`, `AssetDispose.DistributorId` | "Used" (Q1) |

| Object | Change |
|---|---|
| `UDF/dbo.fn_IsVendorUsed` (**new**) | The "used" test |
| `SP_GetAllVendor` | One row per vendor (primary contact via `OUTER APPLY`); + `CanDelete`, `UpdatedAt` |
| `SP_GetVendorById` | Not deleted; + `UpdatedAt` |
| `Sp_SaveVendor` (**new**) | Vendor + primary contact in one transaction under `sp_getapplock`; codes `ok` / `NotFound` / `Stale` / `DupName` / `DupGst` (+ name); company-scoped; user from the token; UTC; status unchanged on edit. `SP_AddEditVendor` / `SP_AddEditDistributorContact` left in the database but no longer called by the API |
| `Sp_ChangeVendorStatus` (**new**), `Sp_DeleteVendor` (**new**) | Status; delete-if-unused |
