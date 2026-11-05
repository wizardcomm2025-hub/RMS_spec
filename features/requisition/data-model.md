# Data model — Requisition

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. One guarded column, one data script.

| Table | Use |
|---|---|
| `Requisition` | Header: `RequisitionNumber`, `RequisitionDate`, `WarehouseId` (from), `ToWarehouseId`, `ReqStatusId` + `RequisitionStatus` (name), `IsApproved`, `ApprovedBy/At`, `ApproveNote`, `IsDeclined`, `DeclinedBy/At`, `DeclineNote`, `CreatedBy/At`, `UpdatedBy/At`, `SerialNo`, `CompanyId`, `IsActive`. **New column** `CancelNote nvarchar(500) NULL`. Edit token `ISNULL(UpdatedAt, CreatedAt)` |
| `RequisitionDetail` | Lines: `StockItemId`, `UOM`, `RequisitionQty`, `ApprovedQty`, `IsActive` |
| `RequistionItemHistory` | Line history on every save (as today) |
| `RequisitionStatus` | 2 Pending approval, 3 Approved, transfer statuses; **new** `Declined`, `Cancelled` rows (data script, by `StatusSystemName`) |
| `Warehouse` (`CanTakeRequisition`), `UserWarehouseMapping`, `ItemWarehouseMap`, `StockItem` | Locations, user's locations, items at a location, units |
| `Transfer` | In-transit / received counts per requisition (read only) |

**Number** (Q3): `REQ/<FY>/<0001>` from the requisition date, next = max suffix for the company + 1 under `sp_getapplock REQ_<CompanyId>`; `SerialNo` keeps the suffix.

| Object | Change |
|---|---|
| `specs/requisition/seed_requisition_statuses.sql` (**data, run once**) | Adds `RequisitionStatus` `Declined` and `Cancelled` when missing (guarded by `StatusSystemName`) |
| `Tables/dbo.Requisition` | Guarded `ADD CancelNote` |
| `UDF/dbo.fn_GetReqStatusId` (**new**) | Status id by system name |
| `SP/dbo.Sp_GetRequisitionList` (**new**) | Paged list with filters, item count, transfer counts, status code, `TotalCount` |
| `SP/dbo.Sp_GetRequisitionSheet` (**new**) | Items at From by type with stock (main), in transit and this requisition's requested qty / unit; header info + edit token |
| `SP/dbo.Sp_SaveRequisitionV2` (**new**) | Create / edit pending; codes `ok / NotFound / Locked / Stale / BadFrom / BadTo / SameLocation / BadDate / NoLine / BadQty / BadItem / BadUnit`; number; lines upsert + delete left-out; optional approve |
| `SP/dbo.Sp_DecideRequisition` (**new**) | Approve / decline pending; codes `ok / NotFound / Locked / Stale / NoNote` |
| `SP/dbo.Sp_CancelRequisition` (**new**) | Cancel pending with reason; codes `ok / NotFound / Locked / NoNote` |
| `SP/dbo.Sp_DuplicateRequisition` (**new**) | New pending requisition from another one (company-checked, never deletes) |
| `SP/dbo.Sp_GetRequisitionDetail` (**new**) | Header + lines for the view page, company-checked |
| `SP/dbo.Sp_SaveRequisition`, `SP_SaveDuplicateRequistion`, `SP_GetReqDetailView`, `SP_GetReqListWarehouseWiseSearch` | Not called by the screens any more (D5) |
