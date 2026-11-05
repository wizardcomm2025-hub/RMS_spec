# Data model — Purchase Order

Tenant DB. **No table schema change.** One guarded data script (new status). Objects in `RMS_API/DBSCRIPT/`.

| Table | Use |
|---|---|
| `PurchaseOrderMaster` | Header: `VendorId`, `PoDate`, `PoNumber`, `Status` ('Saved' / 'Submit' / 'Cancelled'), `Total`, `POLogId`, `IsActive`, `IsDelete`, `CompanyId`. Edit token `ISNULL(UpdatedAt, CreatedAt)` |
| `POHeadStatusLog` | Status history: 9 Draft, 10 Submitted, **PO_Cancelled** (new; `Comment` = reason) |
| `PurchaseOrderDetails` | Lines; `RequisitionLineItemId` links a requisition line |
| `RequisitionLineItem`, `RequisitionLineItemStatusLog`, `RequisitionHeader.POGenarated` | Linked lines move to status 8 (on PO); released lines go back to their previous status and `PoLineItemId = NULL` |
| `ReceiveHead.POId` | "Received" check for cancel |
| `StatusMaster`, `StatusCompanyUIMapping` | New `PO_Cancelled` row + "Cancelled" UI name per company (data script) |

**PO number** (Q2): `PO/<FY>/<0001>` where FY = `2026-27` for 1 Apr 2026 – 31 Mar 2027 (from the PO date). Next = max existing suffix for that company + prefix + 1, under `sp_getapplock` `PO_<CompanyId>`. Legacy numbers are left as they are.

| Object | Change |
|---|---|
| `specs/purchase-order/seed_po_cancelled_status.sql` (**data, run once**) | Adds `StatusMaster` `PO_Cancelled` and its `StatusCompanyUIMapping` "Cancelled" for every company (guarded) |
| `UDF/dbo.fn_GetStatusId` (**new**) | Status id by system name |
| `SP/dbo.Sp_GetPurchaseOrderList` (**new**) | Paged list with filters, total, item count, `HasReceipt` |
| `SP/dbo.Sp_SavePurchaseOrderDetails` | Rewritten: codes, one transaction, draft-only, company-scoped header and lines, line diff (switch off left-out lines, release requisition links), validation, server number, status only 'Saved' / 'Submit', UTC, edit token |
| `SP/dbo.SP_DeletePODetails` | Company + draft only; total recomputed from active lines; requisition line released (kept for older callers) |
| `SP/dbo.Sp_CancelPurchaseOrder`, `SP/dbo.Sp_DeletePurchaseOrder` (**new**) | Cancel submitted; delete draft |
| `SP/dbo.SP_GetPurchaseOrderDetailsById` | + `UpdatedAt` edit token, `HasReceipt` (company check kept); `CREATE OR ALTER`; inactive lines filtered; **fixed**: status name was read with a hard-coded `MAP.CompanyId = 1`, now the caller's company |
| `SP/dbo.Sp_GetPurchaseOrderState` (**new**) | `PoId, PoNumber, UpdatedAt, StatusCode, HasReceipt` for the edit screen |
| `SP/dbo.Sp_ReleasePoRequisitionLines` (**new**, internal) | Reads `#ReleaseReq`; restores each requisition line's previous status, logs "Released from purchase order", clears `PoLineItemId`, resets `POGenarated`. Called by save, line delete, PO delete and cancel |
| `UDF/dbo.fn_GetStatusId` (**new**) | Status id by StatusMaster code |
