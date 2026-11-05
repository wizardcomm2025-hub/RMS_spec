# 02 — Save counts

## Summary
A user with `create_stock_check` saves the counted items. Each saved item gets a count row and its location stock is set to the counted quantity (main unit).

## Main flow
1. Enter counts (and optionally unit / note) for some items.
2. **Save counts (n)** → server checks permission, location (company + user), every item (company, active, mapped to the location), quantity ≥ 0 and unit, then in one transaction under a per-location lock: converts to the main unit, inserts the `StockCheck` row (now, user, company, note) and sets `Stock` (main unit). → "12 counts saved"; the sheet reloads with the new last count and system stock.
3. Row **Save** does the same for one item → "Onion count saved".

## Alternative flows
"Select a location" · "Enter a count for at least one item" · "Counts can't be negative" · "Onion can't be counted in box" · "One or more items aren't available at this location" · "You don't have permission to perform this action."

## Acceptance criteria
1. Only entered rows are sent and saved; blank rows untouched (S1).
2. Count rows and stock rows always carry the caller's company and user (S2).
3. Stock row chosen by item + location + company on the server, never by an id from the request; another company's location → refused (S3).
4. 500 g counted, conversion 1000 → count row 0.5 kg, stock 0.5 kg (S4).
5. Note saved; any failure saves nothing (S5).
6. 0 can be saved (S6).

## Open questions
None.
