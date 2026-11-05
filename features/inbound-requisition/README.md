# Stock → Inbound Requisition

**Inbound Requisition** is the supplying location's side of a requisition: approved requisitions **addressed to my location** (usually the central store), and the **dispatch** of goods against them. A dispatch creates a **transfer** (`dbo.Transfer` + `dbo.TransferDetail`, status *In transit*), takes the quantity out of the sending location's stock and records the vehicle and driver (`dbo.TransferVehicleRelation`). The requesting location then receives it in **Item Receive** (next module).

Direction: requisition **from** = the requester (`Requisition.WarehouseId`), **to** = the supplier (`Requisition.ToWarehouseId`). A transfer goes the other way: `Transfer.FromWarehouseId` = supplier (stock goes down), `Transfer.ToWarehouseId` = requester (stock goes up on receive).

Status (2026-11-03): **Built — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/inbound-requisition/plan.md`](../../../RMS_API/specs/inbound-requisition/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `INB`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search.md`](./01-view-and-search.md) | See approved requisitions addressed to my locations, with what is left to send |
| 02 | [`02-dispatch.md`](./02-dispatch.md) | Dispatch goods against a requisition (one or more transfers until it's fully sent) |

## Defects this feature fixes (found 2026-11-03)

| # | Defect | Fixed by |
|---|---|---|
| I1 | **Transfers from this screen go the wrong way**: it saves `From` = the requester and `To` = the supplier, so dispatch takes stock **out of the requesting kitchen** and Item Receive (which lists transfers whose `To` is the receiver) offers it to the **supplier** | [02](./02-dispatch.md) |
| I2 | Stock is read, reduced and **written back** (`SELECT Qty … SET Qty = old − sent`) with no lock — two dispatches at once lose one deduction; the stock row is found without company and its **unit is overwritten** with the line's unit (no conversion) | [02](./02-dispatch.md) |
| I3 | Nothing is checked: more than approved, more than left, more than in stock, negative quantities, a requisition that isn't approved / of another company / for another location; lines with 0 are saved as transfer lines | [02](./02-dispatch.md) |
| I4 | Transfer numbers `TR03-11-26-001` counted per date **across all companies** | [02](./02-dispatch.md) |
| I5 | The list and the dispatch details load any location / requisition id — no company or user-location check | [01](./01-view-and-search.md) |
| I6 | No transaction (cursor); a failure half-way leaves a transfer with half its lines and half the stock moved | [02](./02-dispatch.md) |
| I7 | No server permission checks | all |
| I8 | One date only, "Get Request Items" button, no paging, transfer qty defaults to 0, no "left to send", vehicle / driver optional, not on the standard design | [01](./01-view-and-search.md), [02](./02-dispatch.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Block over-dispatch**: per item, send ≤ approved − in transit − received | 2026-11-03 |
| Q2 | **Block dispatch above stock** at the sending location (main unit), under the location's stock lock (shared with Stock Check and Wastage) | 2026-11-03 |
| Q3 | **Vehicle and driver required** (the company's active ones); responsible person name / phone optional | 2026-11-03 |
| Q4 | Transfer numbers **`TRF/2026-27/0001`** per company per financial year (from the transfer date); legacy numbers unchanged | 2026-11-03 |
| D1 | List shows requisitions with status Approved (3) and `IsApproved = 1` addressed to the user's locations; "Open" = something left to send | 2026-11-03 (default) |
| D2 | Transfer date: today, or back to the requisition date; not in the future | 2026-11-03 (default) |
| D3 | Quantities are in the requisition line's unit; stock checks / deductions convert to the main unit (alternate ÷ conversion) | 2026-11-03 (default) |
| D4 | Permissions: `viewlist_inbound_requisition` list, `viewdetails_inbound_requisition` dispatch details, `create_item_transfer` dispatch | 2026-11-03 (default) |
| D5 | Old endpoints kept for other callers (mobile), company-guarded; `SaveTransferDetailsV2` goes through the new dispatch (direction taken from the requisition), vehicle / driver not forced there | 2026-11-03 (default) |

## Out of scope
Item Receive and Item Transfer screens (next modules); editing or cancelling a transfer after dispatch; correcting transfers already saved the wrong way (I1) or stock lost by I2 (the pre-check lists them); yield on transfer (stays 0 as today).
