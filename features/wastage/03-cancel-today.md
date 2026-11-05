# 03 — Cancel today's wastage

## Summary
Under the sheet, "Recorded today" lists today's wastage at the location. A user with `delete_wastage` can cancel an entry the same day.

## Main flow
1. List: time · item · qty + unit · reason · barcode · by · Cancel (icon, tooltip "Cancel this entry").
2. Cancel → "Cancel wastage of 0.5 kg Onion? The quantity goes back into stock." → stock +0.5 kg (and the barcode), entry `IsActive = 0` → "Wastage entry cancelled"; the list and sheet reload.

## Alternative flows
Already cancelled / other company → "Wastage entry not found" · from a previous day → "Only today's entries can be cancelled".

## Acceptance criteria
1. Stock and barcode restored exactly; done once only (second call: not found).
2. Cancelled entries no longer count in the Wastage Report or the dashboard wastage widgets (D4).

## Open questions
None.
