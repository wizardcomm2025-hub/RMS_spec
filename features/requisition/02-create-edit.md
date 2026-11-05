# 02 — Create and edit a requisition

## Summary
A user with `create_…` raises a requisition; with `edit_…` changes a **pending** one. One page with an item sheet (same pattern as Stock Check).

## Main flow
1. **＋ New requisition** → page `/admin/item-requisition/create` (edit: `?reqId=`). Header: Requisition date * (today; up to 30 days ahead) · From * (user's locations) · To * (locations taking requisitions, not From). Changing them with typed lines asks "Discard entries?".
2. Inventory type tabs · search · Show (All / Requested). Columns: Item (pinned) · Group · In stock at From · In transit · **Request** (qty + unit, blank by default).
3. Footer: "n items requested" · Cancel · **Submit** · **Submit and approve** (approvers only, confirm).
4. Save: server checks permission, company, locations, date, every line (item at From, qty > 0, unit main / alt), and for an edit that it's still pending and unchanged since opened; in one transaction it writes the header, upserts lines, **deletes lines left out**, logs history, assigns `REQ/<FY>/<n>` on first save. → "Requisition REQ/2026-27/0001 submitted" / "… submitted and approved".

## Alternative flows
"Select the from location" · "Select the to location" · "From and to must be different locations" · "Requisition date must be today or within 30 days" · "Request at least one item" · "Quantity must be greater than 0" · "{item} can't be requested in {uom}" · "One or more items aren't available at this location" · "Only pending requisitions can be changed." · "This requisition was changed by someone else. Reload and try again." · "You don't have permission to perform this action."

## Acceptance criteria
1. Nothing outside the caller's company is read or changed (R1, R2).
2. Approved / declined / cancelled / in-transit requisitions can't be saved (R2).
3. Clearing a line's quantity and saving removes the line (R6).
4. Numbers sequential per company per FY, no repeats under parallel saves (R5).
5. Any failure saves nothing (R7). Edit opens by URL (no `sessionStorage`), real inventory types (R9).

## Open questions
None.
