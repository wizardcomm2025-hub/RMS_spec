# UI / UX — Production

Follows `UI_UX_STANDARDS.md` §12 (list) and the sheet page pattern (requisition / dispatch / receive).

## List (`/admin/production`)
`PageHeader` "Production" (no description; About links: Recipe, Stock Report) with **New production** (`create_product`). Filter bar: search · date range (Apply) · Location. Columns: Date · Item · Quantity · Location · Ingredient ₹ · Adjustments ₹ · Overhead ₹ · Final ₹ · Unit ₹ · By · Actions (View). Money right-aligned, ₹ with 2 decimals; paging footer; phone cards; skeletons; empty / no match / error / no location states.

## New production (`/admin/production/add`)
```
PageHeader  Production (link) › New                        [←] New production
┌ card ─────────────────────────────────────────────────────────────────────┐
│ PRODUCTION  Item * [▾ search]  Location * [▾]  Date * [ ]  Quantity * [  ] kg│
│             Recipe makes 5 kg (yield 90%)                                    │
│ OVERHEAD    Overhead ₹ [   ]                                                 │
└────────────────────────────────────────────────────────────────────────────┘
┌ table: Ingredient (pinned) · Unit · In stock here · Standard · Actual [ ] · Variance · Cost ₹ · Adjustment ₹ [ ] · Reason [ ] ┐
Sticky footer: Ingredients ₹ · Adjustments ₹ · Overhead ₹ · Final ₹ · ₹/kg        [Cancel] [Save production]
```
- Table shows "Pick an item and a location to load the recipe." until both are chosen.
- Actual above In stock: red outline + "Only 2 kg in stock"; variance in warning colour when ≠ 0; reason required (red outline) when an adjustment is entered.
- Confirm before saving; discard prompt / `beforeunload` when changed; Save disabled while loading.
- 360 px: fields stack; table scrolls sideways with the ingredient pinned; footer totals wrap above full-width buttons.

## View (`/admin/production/view?id=`)
Same layout read-only: summary card (item, quantity, location, date, by) + cost summary; ingredient table with Standard / Actual / Variance / Cost / Adjustment / Reason; **Print** button (existing PDF layout).

Dates in the company format; theme tokens only.
