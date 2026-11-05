# API contract — Requisition

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`. Permissions are `…_requisition_for_stock_transfer`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetRequisitionList` (**new**) | `viewlist_` | `{ pageNumber, pageSize, fromDate, toDate, status, fromWarehouseId, toWarehouseId, search }` → rows + `totalCount` |
| `/Setting/GetRequisitionSheet` (**new**) | `create_` or `edit_` | `{ requisitionId?, fromWarehouseId, toWarehouseId, inventoryTypeId }` → header (`requisitionId, requisitionNumber, requisitionDate, fromWarehouseId, toWarehouseId, statusCode, updatedAt`) + items |
| `/Setting/SaveRequisitionV2` (**new**) | `create_` (new) / `edit_` (edit); `approve_` when `approve: true` | `{ requisitionId, requisitionDate, fromWarehouseId, toWarehouseId, approve, updatedAt, lines: [{ stockItemId, qty, uom }] }` (max 1000) → `requisitionId, requisitionNumber, updatedAt` |
| `/Setting/DecideRequisition` (**new**) | `approve_` | `{ requisitionId, approve, note, updatedAt }` |
| `/Setting/CancelRequisition` (**new**) | `delete_` | `{ requisitionId, reason }` |
| `/Setting/DuplicateRequisition` (**new**) | `create_` | `{ requisitionId, requisitionDate, fromWarehouseId, toWarehouseId }` → new `requisitionId, requisitionNumber` |
| `/Setting/GetRequisitionDetail` (**new**) | `viewdetails_` | `{ requisitionId }` → header (+ status, notes, can-do flags) + lines |
| `/Setting/GetRequisitionToLocations` (**new**) | `viewlist_` | → active company locations that take requisitions |
| `/Setting/SaveRequisitionWithDetails`, `/Setting/SaveDuplicateRequistion` | as above | Kept; mapped to the new procedures |
| `/Setting/GetReqListKitSearch`, `GetReqListV2`, `GetReqDetailForView`, `GetToWarehouseByUser`, `GetTransferListByReqId` | `viewlist_` / `viewdetails_` | Kept; `[CompanyAuthorize]` |

Messages (`status: false`): as in [02](./02-create-edit.md)–[04](./04-cancel-duplicate.md), plus `Requisition not found` and `Something went wrong. Please try again.`. 401 for no / invalid / SuperAdmin token.
