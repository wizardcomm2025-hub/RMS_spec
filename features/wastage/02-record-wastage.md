# 02 — Record wastage

## Summary
A user with `create_wastage` saves the entered lines. Each line reduces the location's stock (main unit) and adds a wastage row.

## Main flow
1. Enter qty, unit (main / alternate) and reason for some items.
2. **Save wastage (n)** → confirm "Record wastage for 3 items at Newtown? Stock goes down by these quantities." → server checks permission, location (company + user), items (company, active, at the location), reason (company, active), qty > 0, unit, barcode (company, item, location, unit, enough left) and stock on hand; in one transaction under the location lock: converts to the main unit, adds wastage rows, reduces stock and barcode. → "3 wastage entries saved"; the lines clear; the sheet and "Recorded today" reload.
3. Row **Save** does the same for one item (no confirm) → "Onion wastage saved".

## Alternative flows
"Select a location" · "Enter a wastage quantity for at least one item" · "Quantity must be greater than 0" · "Select a reason for every line" · "Onion can't be recorded in box" · "Only 4 kg of Onion in stock" · "Barcode 59683636 doesn't have enough left" · "One or more items aren't available at this location" · "You don't have permission to perform this action."

## Acceptance criteria
1. Stock row found on the server by item + location; never by an id from the request (W1).
2. 500 g with conversion 1000 → stock −0.5 kg, wastage row 0.5 kg (W2, D2).
3. Missing / inactive / other company's reason refused (W3).
4. More than stock refused, also for two users at once (W4).
5. Barcode of another company / item / location refused (W5).
6. Any failure saves nothing (W6). Lines clear after a successful save, so a second click records nothing (W7).

## Open questions
None.
