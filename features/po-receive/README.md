# Purchase → PO Receive

A **PO receive** (goods receipt, GRN) records one delivery from a vendor against a submitted **purchase order**: which items arrived, how many, how many were damaged, at what cost, with the invoice / challan. Posting it adds the good quantity to stock at the chosen location and records the purchase price. Header `dbo.ReceiveHead`, lines `dbo.PoReceivedDetails`, stock `dbo.Stock`, price history `dbo.Item_Price_History`.

Statuses: **Draft** (`IsDraft = 1`, editable, no stock effect) → **Posted** (`IsDraft = 0`, stock updated, final).

Status (2026-11-03): **Built — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/po-receive/plan.md`](../../../RMS_API/specs/po-receive/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `PRC`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search.md`](./01-view-and-search.md) | See receives page by page; filter by date, status, vendor, location; search |
| 02 | [`02-receive-goods.md`](./02-receive-goods.md) | Record a delivery against a PO, save as draft, post it to stock |
| 03 | [`03-delete-draft.md`](./03-delete-draft.md) | Delete a draft receive |
| 04 | [`04-po-receipt-state.md`](./04-po-receipt-state.md) | See on the PO list how much of each PO has arrived |

## Defects this feature fixes (found 2026-11-02)

| # | Defect | Fixed by |
|---|---|---|
| R1 | **Cross-company receipt:** the save finds the PO by number only (`WHERE PoNumber = @PoNo`). PO numbers are now `PO/<FY>/<n>` per company, so every company has `PO/2026-27/0001` — a receive can attach to **another company's PO** | [02](./02-receive-goods.md) |
| R2 | Only the **user who created the PO** can receive it, and only the creator can open a receive (`CreatedBy = @UserId` filters) | [02](./02-receive-goods.md) |
| R3 | **Damaged quantity goes into stock**; quantities in the alternate unit (g, box) are added to stock in the main unit (kg) **without conversion** | [02](./02-receive-goods.md) |
| R4 | **Over-receipt** is checked only in the browser and only within one receive; a second receive for the same PO doesn't know what already arrived | [02](./02-receive-goods.md) |
| R5 | A posted receive can be saved again through the API and **adds stock again**; the update isn't company-checked (`WHERE ReceiveHeadId = @id`) | [02](./02-receive-goods.md) |
| R6 | Invoice and challan files are picked on screen but **never sent**; they are silently lost | [02](./02-receive-goods.md) |
| R7 | Location is optional → stock rows with **no location** | [02](./02-receive-goods.md) |
| R8 | Vendor is editable and can differ from the PO's vendor | [02](./02-receive-goods.md) |
| R9 | Amounts are typed twice (header value / discount / 3 taxes **and** per-line cost / tax / total) and never reconciled | [02](./02-receive-goods.md) |
| R10 | List isn't paged, filters by creation time, breaks with a half-picked date range, shows raw errors, hard-coded colours, "Submit" badge; not on the §12 recipe | [01](./01-view-and-search.md) |
| R11 | No server permission checks; `GetPORecDetailById` has no `[Authorize]` at all | all |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **One receive per delivery.** Each delivery (invoice / challan) is its own receive: Draft → Posted. A PO can have many receives. Remaining = ordered − all **posted** receives | 2026-11-02 |
| Q2 | **Stock gets received − damaged**, converted to the raw material's **main unit** (alternate unit ÷ `Conversion`). Damaged qty is kept on the receive for vendor follow-up. A line in any other unit is refused | 2026-11-02 |
| Q3 | **Over-receipt is blocked** on the server: receiving ≤ ordered − already posted (drafts don't reserve). Extra goods need a new PO | 2026-11-02 |
| Q4 | **Amounts from lines**: unit cost (prefilled from the PO, editable) and tax amount per line; line amount = receiving × cost + tax. Receive total = lines − optional discount, **prefilled but editable** to match the invoice; a difference is shown, not blocked | 2026-11-02 |
| D1 | Receive number by the server: `GRN/<FY>/<0001>` per company (same rules as PO numbers) | 2026-11-02 (default) |
| D2 | **Posted receives are final** — no edit, no delete. Corrections later through a vendor return (not in this scope). Drafts can be edited and deleted | 2026-11-02 (default) |
| D3 | Vendor comes from the PO (read-only). Location required (company's active location). Received date required, not in the future, not before the PO date | 2026-11-02 (default) |
| D4 | To post: invoice no **or** challan no required; the same invoice no can't be used twice for the same vendor | 2026-11-02 (default) |
| D5 | Only **Submitted** POs with something left can be received; anyone with `receive_receive_asset` can receive (no creator restriction) | 2026-11-02 (default) |
| D6 | Old endpoints (`GetPORecList`, `GetPORecDetailById`, `GetPurchaseOrderReceive`) stay for other callers, company-guarded; the screens use the new ones | 2026-11-02 (default) |

## Out of scope
Vendor returns / debit notes; reversing a posted receive; receiving items not on the PO; invoice scan (OCR) on receive (button stays hidden); fixing stock already wrong from R3 (the pre-check lists it).

## Found, not fixed
| Item | Note |
|---|---|
| Upload folder from the browser | Attachments use the existing upload service, which takes the folder path (`ImageFilePath`, the company's image path) from the request, as Recipe and Checklist do. The server should read it from the company instead |
| Old clients editing a draft | Saving an existing draft now needs the edit token (`updatedAt`); a client that doesn't send it gets "This receipt was changed by someone else" |
| Legacy POs with the same item on two lines | Old receive lines have no PO line link and count against every line of that item on the PO |

## Permissions (corrected 2026-11-03)
PO Receive uses the existing **"receive_asset"** permission group in `Meta_Permission` (the same codes the sidebar uses): `viewlist_receive_asset` (list), `viewdetails_receive_asset` (open / view a receipt), `receive_receive_asset` (new receipt, post, edit or delete a draft). The `*_po_receive` codes first written in this spec don't exist in the database and were replaced.
