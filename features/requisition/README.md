# Stock → Requisition for Stock Transfer

A **requisition** is a request from one location (the **from** location — usually a kitchen or outlet) to another (the **to** location — usually the central store) for items to be sent over. Header `dbo.Requisition`, lines `dbo.RequisitionDetail`, history `dbo.RequistionItemHistory`. Once **approved** it appears in **Inbound Requisition** at the to-location, which sends the goods (**Item Transfer**) and the from-location receives them (**Item Receive**).

Statuses (`RequisitionStatus`): **Pending approval** (2) → **Approved** (3) → transfer statuses (set by Item Transfer) · or **Declined** · **Cancelled** (new, data script).

Status (2026-11-03): **Built — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/requisition/plan.md`](../../../RMS_API/specs/requisition/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `REQ`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search.md`](./01-view-and-search.md) | See requisitions page by page; filter by date, status, from / to location; search by number |
| 02 | [`02-create-edit.md`](./02-create-edit.md) | Create or edit a pending requisition on an item sheet and submit it |
| 03 | [`03-approve-decline.md`](./03-approve-decline.md) | Approve or decline a pending requisition |
| 04 | [`04-cancel-duplicate.md`](./04-cancel-duplicate.md) | Cancel a pending requisition; duplicate any requisition into a new one |

## Defects this feature fixes (found 2026-11-03)

| # | Defect | Fixed by |
|---|---|---|
| R1 | **"Submit and Approve" re-statuses every approved requisition of that date in every company** (`UPDATE Requisition … WHERE IsApproved = 1 AND RequisitionDate = @date`) when the user has a default warehouse | [02](./02-create-edit.md) |
| R2 | Edits update `WHERE RequisitionId = @id` and lines `WHERE RequisitionDetailId = @id` — **another company's requisition or line can be changed**; approved / in-transit requisitions can still be edited through the API | [02](./02-create-edit.md) |
| R3 | **Anyone can approve their own requisition**; `ApprovedBy` comes from the request; the approve permission is never checked | [03](./03-approve-decline.md) |
| R4 | **"Duplicate" hard-deletes** an existing requisition (and its lines and history) for the same day and locations — even approved or transferred ones — and can copy another company's lines | [04](./04-cancel-duplicate.md) |
| R5 | Numbers are date + serial counted **across all companies** (`MAX(SerialNo) WHERE RequisitionDate = @d`), so they jump and can repeat under load | [02](./02-create-edit.md) |
| R6 | A line set back to 0 is never removed (the screen sends only qty > 0 lines and the save never deletes), so **an item can't be taken off** a requisition | [02](./02-create-edit.md) |
| R7 | Locations, items and units aren't checked (company, user's location, from ≠ to, item / unit); quantity text "00.00" default; `DeclineNote` is `nvarchar(1)`; no transaction | [02](./02-create-edit.md) |
| R8 | No server permission checks; the view loads any requisition id (no company check) | all |
| R9 | List: one date only, "Search" box does nothing (placeholder "Enter Menu Section"), Range / Requisition No radio does nothing, not paged; create flow keeps ids in `sessionStorage`; inventory type hard-coded `52` | [01](./01-view-and-search.md), [02](./02-create-edit.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Approval needs `approve_requisition_for_stock_transfer`.** Submit → Pending approval; an approver approves or declines (decline needs a note). "Submit and approve" only for approvers | 2026-11-03 |
| Q2 | **Many requisitions per day** between the same locations; duplicating never deletes anything | 2026-11-03 |
| Q3 | Numbers **`REQ/2026-27/0001`** per company per financial year (Apr–Mar, from the requisition date), assigned on first save; legacy numbers unchanged | 2026-11-03 |
| Q4 | **Pending requisitions can be edited or cancelled** (cancel needs a reason); approved ones are locked | 2026-11-03 |
| D1 | From = one of the user's locations; To = an active company location that takes requisitions (`CanTakeRequisition`), not the same as From | 2026-11-03 (default) |
| D2 | Requisition date: today up to 30 days ahead (edits keep an earlier date) | 2026-11-03 (default) |
| D3 | Items: active items of the company mapped to the **from** location, by inventory type; quantity > 0 in the main or alternate unit (stored as entered, as today) | 2026-11-03 (default) |
| D4 | Permissions: `viewlist_…` list, `viewdetails_…` view, `create_…` new, `edit_…` edit pending, `delete_…` cancel pending, `approve_…` approve / decline / submit-and-approve (all `…_requisition_for_stock_transfer`) | 2026-11-03 (default) |
| D5 | Old endpoints kept for other callers (mobile), company-guarded, permission-checked; `SaveRequisitionWithDetails` and `SaveDuplicateRequistion` go through the new procedures | 2026-11-03 (default) |

## Out of scope
Inbound Requisition, Item Transfer, Item Receive (next modules); consumption suggestions (the "Preview / consumption" dialog stays as is); fixing requisitions already re-statused by R1 or deleted by R4 (the pre-check lists suspects).

## Found, not fixed
| Item | Note |
|---|---|
| Old procedures | `Sp_SaveRequisition` (with the mass re-status), `SP_SaveDuplicateRequistion` (with the delete) and `SP_GetReqDetailView` (no company check) stay in the database; the API no longer calls the first two. `GetReqDetailForView` still uses `SP_GetReqDetailView` for the per-line in-transit / received quantities on the view page, behind the new company-checked detail call |
| Older callers' edits | `SaveRequisitionWithDetails` (mobile) edits without an edit token, as before |
| Transfer history dialog | The view page reuses the existing transfer history dialog as it was |
