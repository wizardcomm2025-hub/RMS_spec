# 01 — See the business figures

## Summary
A user with `view_dashboard` sees KPI cards, a daily trend and two lists for their locations and the chosen dates. Each part appears only if their permissions allow it.

## Main flow
1. Dashboard opens after sign-in with location **All my locations** and **this month**. Each section loads on its own.
2. **KPI cards** (only those allowed):
   | Card | Figure | Second line | Kind | Opens |
   |---|---|---|---|---|
   | Sales | total including tax | "n bills · average ₹x" + "₹y unpaid" when any | Period | — |
   | Stock value | value of current stock | "n items" | Right now | Stock Report |
   | Low / out of stock | low + out items | "n low · m out" | Right now | Stock Report |
   | Wastage | value wasted | "n entries" | Period | Wastage Report |
   | Production | total production cost | "n batches" | Period | Production |
   | Open purchase orders | value of submitted POs not fully received | "n POs · All locations" | Right now | Purchase Order |
3. **Sales and costs by day**: one bar per day for each allowed series (Sales, Production, Wastage), with a legend; days with nothing show as 0. Ranges above 62 days are grouped by month.
4. **Items to reorder**: the 8 most urgent items (Out first, then furthest below reorder level): item, location, quantity, reorder level, status badge; "View all" opens the Stock Report.
5. **Top wasted items**: the 5 highest by value in the period: item, quantity, value; "View all" opens the Wastage Report.
6. Changing the location refreshes everything except Open purchase orders. Applying new dates refreshes period figures only. Refresh in the header reloads all.

## Alternative flows
- A section fails to load → it shows "Couldn't load this. Retry" and the others are unaffected.
- No data → the card shows ₹0.00 / 0; lists show their empty text ([`ui-ux.md`](./ui-ux.md)).
- User has no locations → location-based sections show "You aren't assigned to any location. Ask your admin to add you in User List."; purchases still show.
- User has `view_dashboard` only → "Nothing to show for your role yet. Ask your admin for access to reports."
- No `view_dashboard` → the standard access-denied page; endpoints answer "You don't have permission to perform this action."

## Acceptance criteria
1. Totals have no row cap (H1, H2): counts and values equal the module screen for the same filters (D1).
2. Only the user's active locations count; another location id returns nothing (H3, Q3).
3. Quantities are in the main unit; alternate-unit rows are converted (H8).
4. No arrow or percentage appears anywhere (H9, D5).
5. Each card says "Right now" or the period; dates don't change "Right now" figures (Q3).
6. Money uses the company currency; dates use the company format.
7. Server checks each section's permission (Q4, H12).
8. Responsive at 360–1920 px with no sideways page scroll.

## Open questions
None.
