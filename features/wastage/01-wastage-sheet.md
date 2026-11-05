# 01 — Wastage sheet

## Summary
A user with `viewlist_wastage` opens Stock → Wastage, picks a location and an inventory type and sees the items in stock there.

## Main flow
1. Filter bar: Location (searchable, user's locations) · Scan barcode · Search (item or group) · Show: All / Entered now. Inventory type tabs.
2. Columns: Item (pinned) · Group · In stock (main unit) · **Wastage** (qty + unit) · **Reason** (searchable, required; "+" adds a reason with `create_reason_of_wastage`) · After (stock left) · row Save.
3. Barcode scan: found → the sheet filters to that item, the barcode chip shows on the row ("Barcode 59683636 · 4 kg left"). Not found / other location / used up → "Barcode not found at this location".
4. "Recorded today" panel under the sheet (use case 03).

## Alternative flows
No location: "You aren't assigned to any location. Ask an admin to add you in User List." · No items: "Nothing in stock at this location for this type." · Error + Retry.

## Acceptance criteria
1. One call returns the sheet; only the caller's company and their location (W8).
2. Wastage inputs start blank; "After" updates as you type and turns red with "Only 4 kg in stock" when over (Q1).
3. Changing location / tab with typed lines asks "Discard entries?".
4. 360 px: phone cards.

## Open questions
None.
