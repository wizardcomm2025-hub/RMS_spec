# 01 — Productions

## Summary
Users with `viewlist_product` see productions recorded at their locations; `viewdetails_product` opens one.

## Main flow
1. Production (menu). Filters: search (item name) · date range (production date, Apply; default this month) · Location (my locations; "All my locations"). Header button **New production** (`create_product`; greyed with a reason without it).
2. Columns: Date · Item · Quantity (with unit) · Location · Ingredient cost · Adjustments · Overhead · Final cost · Unit cost · By · Actions (**View**).
3. Paging footer; phone cards; loads on open.
4. **View** → page `/admin/production/view?id=`: header (item, quantity, location, date, by, at) and cost summary; ingredient table: Ingredient · Unit · Standard · Actual · Variance · Cost · Adjustment · Reason; **Print** (existing PDF layout).

## Alternative flows
Empty: "No productions yet at your locations." + New production · No match + Clear filters · Error + Retry · no location: "You aren't assigned to any location. Ask an admin to add you in User List." · View of another company / location: "Production not found" → back to the list.

## Acceptance criteria
1. Date range always applied; only the caller's company and locations (P9, D4).
2. Totals match the view page; costs in ₹ with 2 decimals.

## Open questions
None.
