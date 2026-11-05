# 01 — Count sheet

## Summary
A user with `viewlist_stock_check` opens Stock → Stock Check, picks a location and an inventory type and sees every item to count.

## Main flow
1. Location (searchable, the user's locations; default first) · inventory type tabs · search (item or group) · Show: All / Counted now / With variance.
2. Columns: Item (pinned) · Group · System stock (main unit) · Last count (qty, date · time, by) · **Count** (input + unit main / alt) · Variance (qty and %, coloured) · Note · row Save.
3. Footer (sticky): "n items counted" · Discard · **Save counts (n)**.

## Alternative flows
No location: "You aren't assigned to any location. Ask an admin to add you in User List." · No items: "No items for this location and type. Map raw materials to the location in Raw Material." · Error: "We couldn't load the count sheet. Check your connection and try again." + Retry.

## Acceptance criteria
1. One call returns the sheet (no C# merging); only the caller's company, location in their locations (S3, S7).
2. Count inputs start **blank** (S1). Variance appears as soon as a count is typed.
3. Changing location / type / tab with unsaved counts asks "Discard counts?".
4. 360 px: phone cards with the same fields; no page sideways scroll.

## Open questions
None.
