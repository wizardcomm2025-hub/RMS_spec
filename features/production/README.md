# Production

**Production** records that the kitchen made a quantity of a **recipe** (a semi-finished or finished item with ingredient lines, see the Recipe feature) at a location. Saving takes each ingredient's **actually used** quantity out of that location's stock, adds the produced quantity to the location's stock of the recipe item, and stores the cost: ingredient cost + adjustments + overhead = **final cost**, and final cost ÷ quantity = **unit cost**.

Status (2026-11-03): **Built — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/production/plan.md`](../../../RMS_API/specs/production/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `PRD`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search.md`](./01-view-and-search.md) | See productions at my locations; open one |
| 02 | [`02-record-production.md`](./02-record-production.md) | Record a production: item, location, quantity, actual ingredient use, adjustments, overhead |

## Defects this feature fixes (found 2026-11-03)

| # | Defect | Fixed by |
|---|---|---|
| P1 | The produced quantity is added only by `UPDATE Stock` — when the location has **no stock row** for the item (the first production there) **nothing is added**, silently | [02](./02-record-production.md) |
| P2 | Ingredients are taken at the **standard** quantity; the "actual" inputs on the screen are bound to the standard value and write into the yield **percentage**, so actual use can't be entered and `ActualYield` saves a percentage | [02](./02-record-production.md) |
| P3 | When an ingredient would go negative its stock is **silently set to 0** (the shortfall vanishes); the screen's own check uses a stock sum that mixes units | [02](./02-record-production.md) |
| P4 | Stock read-modify-write with no lock (a parallel Wastage / Stock Check / Dispatch / Receive loses an update); unit conversion guessed from the line unit vs the stock row unit, wrong when a stock row is kept in the alternate unit; an ingredient with no stock row aborts the save with a raw SQL message | [02](./02-record-production.md) |
| P5 | Ingredients, quantities and **all costs come from the browser** and are saved as sent; the scale ignores the recipe yield (recipe cost uses batch × yield) | [02](./02-record-production.md) |
| P6 | No company / location check: any location id, any item, ingredients that aren't on the recipe; ingredient stock / cost lookups not company-scoped | [02](./02-record-production.md) |
| P7 | The date is converted to UTC before saving, so an evening production in India is saved **on the previous day** | [02](./02-record-production.md) |
| P8 | `ProductionId <> 0` (edit) takes the ingredients again and inserts the lines again without changing the header | [02](./02-record-production.md) |
| P9 | The list ignores the date range unless a production exists exactly on the from-date; lists every location of the company; no total count | [01](./01-view-and-search.md) |
| P10 | No server permission checks; a POS-mapping lookup is called after every save for nothing | all |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Actual consumption, prefilled**: per ingredient the standard quantity (recipe line × quantity ÷ (batch × yield)) is shown and prefilled as Actual; stock goes down by Actual; standard vs actual (variance) is stored | 2026-11-03 |
| Q2 | **Block short stock**: Actual above the ingredient's stock at the location (main unit) refuses the save, naming the item | 2026-11-03 |
| Q3 | **Server calculates the cost**: line cost = Actual × the item cost Recipe uses (`fn_GetItemUnitCost`); + line adjustments + overhead = final; unit cost = final ÷ quantity. The page only previews | 2026-11-03 |
| Q4 | **View only** after saving: no edit, no cancel; mistakes are corrected with Wastage / Stock Check | 2026-11-03 |
| D1 | Producible items: the company's active non-raw items with at least one active recipe line | 2026-11-03 (default) |
| D2 | Quantities in the **main unit** of each item (produced quantity in the recipe item's unit, ingredients in their own main unit — the unit stock and cost use); the recipe batch (`RecipeQty`) is taken in the recipe item's main unit; yield 0 or above 100 counts as 100 (as in Recipe cost) | 2026-11-03 (default) |
| D3 | Production date: today by default; not in the future; stored as the date chosen (no time-zone shift) | 2026-11-03 (default) |
| D4 | Location: one of the user's locations; list and view limited to the user's locations | 2026-11-03 (default) |
| D5 | Line adjustment (₹, may be negative) needs a reason; overhead ≥ 0; final cost may not go below 0 | 2026-11-03 (default) |
| D6 | Stock lock `SCK_<location>` (shared with Stock Check, Wastage, Dispatch, Receive); a missing stock row for the produced item is created in its main unit | 2026-11-03 (default) |
| D7 | Permissions: `viewlist_product` list, `viewdetails_product` view, `create_product` record | 2026-11-03 (default) |
| D8 | Old endpoints kept, company-guarded with permissions; `AddEditProduction` goes through the new save with Actual = standard (old callers never sent a usable actual) and edit (`ProductionId <> 0`) refused | 2026-11-03 (default) |

## Out of scope
Editing or cancelling a production; production numbers (the id is shown); producing at a location the user isn't mapped to; nested production of semi-finished ingredients; changing the recipe item's stored cost; correcting stock already lost by P1 / P3 (the pre-check lists suspects).
