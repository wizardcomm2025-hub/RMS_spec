# Purchase → Purchase Order

A **purchase order (PO)** is an order the company places with a vendor for raw materials. Header `dbo.PurchaseOrderMaster` (vendor, date, number, total, status via `POHeadStatusLog`), lines `dbo.PurchaseOrderDetails` (item, qty, unit, cost, description, optional requisition line). Lines can come from approved **requisition lines** (`RequisitionLineItem`) or be added by hand or from an **invoice scan (OCR)**. Goods are received against a PO in PO Receive (`ReceiveHead.POId`).

Statuses: **Draft** (`Saved`, status 9) → **Submitted** (status 10) → optionally **Cancelled** (new). Receiving does not change the PO status.

Status (2026-11-02): **Built — SQL not yet run on dev; testing deferred.** Use case 05 (email to vendor, 2026-11-17): **Built — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/purchase-order/plan.md`](../../../RMS_API/specs/purchase-order/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `PO`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search.md`](./01-view-and-search.md) | See POs page by page; filter by date, status, vendor; search by number |
| 02 | [`02-create-edit-submit.md`](./02-create-edit-submit.md) | Create or edit a draft PO and submit it |
| 03 | [`03-cancel-submitted.md`](./03-cancel-submitted.md) | Cancel a submitted PO that has nothing received |
| 04 | [`04-delete-draft.md`](./04-delete-draft.md) | Delete a draft PO |
| 05 | [`05-email-to-vendor.md`](./05-email-to-vendor.md) | Email a submitted PO to the vendor as a PDF |

## Defects this feature fixes (found 2026-11-02)

| # | Defect | Fixed by |
|---|---|---|
| D1 | **Cross-company takeover:** the edit runs `UPDATE PurchaseOrderMaster … WHERE PoId = @id` and sets `CompanyId` to the caller's | [02](./02-create-edit-submit.md) |
| D2 | Line edits `WHERE PoDetailsId = @id` only — a line of another PO / company can be changed | [02](./02-create-edit-submit.md) |
| D3 | **Submitted (even received) POs can be edited** through the API; the status is free text from the client | [02](./02-create-edit-submit.md) |
| D4 | **Duplicate PO numbers:** date text from the screen + `RAND()` | [02](./02-create-edit-submit.md) |
| D5 | Removing a line twice **subtracts its amount twice** from the total; no company check; works on submitted POs; the requisition line isn't released | [02](./02-create-edit-submit.md) |
| D6 | Vendor, items and requisition lines aren't checked (company, active, raw material; a requisition line already on another PO can be linked again) | [02](./02-create-edit-submit.md) |
| D7 | No transaction (header saved, lines half-saved); XML document never released; zero / negative qty or cost accepted | [02](./02-create-edit-submit.md) |
| D8 | No server permission checks (`create_purchase_order`, `edit_purchase_order`); SuperAdmin tokens pass; `GETDATE()`; raw error text; no stale-save check | all |
| D9 | No way to cancel or delete a PO | [03](./03-cancel-submitted.md), [04](./04-delete-draft.md) |
| D10 | The list loads every PO in the date range; not on the recipe | [01](./01-view-and-search.md) |
| D11 | **No way to send a PO to the vendor**: the UI has `sendPoNotification` but nothing calls it | [05](./05-email-to-vendor.md) |
| D12 | The old `Notification/SendPONotification` emails only a link to `NonLogin/GetPurchaseOrderview?PoId=<base64 id>`, a page the Next.js app doesn't have. It depends on a per-company `CompanySecret` / notification template that is usually missing, and fails silently | [05](./05-email-to-vendor.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | Only **Draft** POs can be edited. A **Submitted** PO can be **cancelled with a reason** while nothing has been received (no `ReceiveHead`, draft or not); its requisition lines go back to pending | 2026-11-02 |
| Q2 | **PO number made by the server**, sequential per company per **financial year (Apr–Mar)**: `PO/2026-27/0001`. Assigned on the first save; never changes | 2026-11-02 |
| Q3 | **Draft POs can be deleted**; their requisition lines are released | 2026-11-02 |
| Q4 | **Server paging + filters**: date range (default this month), status, vendor, PO-number / vendor search; exact total | 2026-11-02 |
| Q5 | Lines: raw materials of the company that are active (or already on this PO), qty > 0, cost ≥ 0, unit = the item's main or alternate unit; at least one line; vendor active (or already on this PO). Removing a line in the form only changes the form; the save switches off left-out lines (as Recipe) | 2026-11-02 (default) |
| Q6 | Cancel needs `edit_purchase_order`; delete draft needs `edit_purchase_order` (no separate delete permission exists) | 2026-11-02 (default) |
| Q7 | The vendor gets the PO as a **PDF attachment** made on the server (Select.HtmlToPdf, already installed); no public link | 2026-11-17 |
| Q8 | Sent by an **Email to vendor** button on **Submitted** POs, any time and re-sendable; not automatic on Submit | 2026-11-17 |
| Q9 | A **dialog prefilled** with the vendor's contact emails (To), the PO creator (CC), subject and message; all editable | 2026-11-17 |
| Q10 | Sent from the **platform mailbox** (Central `PlatformNotificationSecret`); From name = restaurant, Reply-To = sender | 2026-11-17 |
| Q11 | Needs `create_purchase_order` (the people who raise POs send them; no new permission) | 2026-11-17 (default) |
| Q12 | Every send is logged in a new `PurchaseOrderEmailLog`; the dialog shows "Last emailed …" | 2026-11-17 (default) |

## Depends on

Vendor (active vendors), Raw Material, Item Requisition (approved lines), PO Receive, notifications on submit.

## Found, not fixed
| Item | Note |
|---|---|
| Anonymous PO endpoints | `Setting/GetAllPoTemplate`, `GetPoVendorWarehouseDetails`, `GetGetPurchaseOrderDetailsbyIdForview`, `GetCompanyDetails` have no `[CompanyAuthorize]`. They appear to serve the vendor's emailed PO view link, so they were left as they are. Anyone with a PO id can read that PO. A follow-up should replace the raw id with a signed, expiring link token. |
| Old PO email | `Notification/SendPONotification`, `SP_GetUsersToPoSendNotification` and the UI's unused `sendPoNotification` stay as they are (not called). Remove them in a clean-up. |
| Hard-coded mail fallback | `InvoiceService.SendEmail` falls back to a hard-coded Gmail account and password. Not touched here; it should move to configuration. |
