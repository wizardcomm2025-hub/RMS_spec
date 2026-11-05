# Inventory → Recipe

A **recipe** is something the kitchen makes from ingredients. It is a `dbo.StockItem` row of a non-raw inventory type: **Semi-Finished** (made for the kitchen, e.g. gravy, dough) or **Finished** (sold to guests on the POS). Its ingredient lines are `dbo.ReceipeDetail` rows (`ParentStockId` = the recipe): raw materials and semi-finished recipes, each with a quantity and unit. A recipe also has a batch size and yield, a category, the locations that make or use it, and — for finished items — a menu section, POS unit / conversion, price, veg flag, image and POS item mapping.

Status (2026-10-30): **Built** — approved 2026-10-30; DBSCRIPT files not yet run on dev; manual tests deferred. Plan: [`RMS_API/specs/recipe/plan.md`](../../../RMS_API/specs/recipe/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `RCP`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search.md`](./01-view-and-search.md) | See recipes by type, page by page; search; filter |
| 02 | [`02-add-or-edit.md`](./02-add-or-edit.md) | Add or edit a recipe with its ingredients |
| 03 | [`03-recipe-cost.md`](./03-recipe-cost.md) | Recipe cost: calculated from ingredients, optional manual override |
| 04 | [`04-switch-on-off.md`](./04-switch-on-off.md) | Deactivate / reactivate a recipe |
| 05 | [`05-delete-unused.md`](./05-delete-unused.md) | Delete a recipe that has never been used |

## Defects this feature fixes (found 2026-10-30)

| # | Defect | Fixed by |
|---|---|---|
| D1 | **Cross-company overwrite:** the edit runs `UPDATE StockItem … WHERE StockItemId = @id` and sets `CompanyId` to the caller's | [02](./02-add-or-edit.md) |
| D2 | **Decimals lost:** conversion, cost, price, reorder, shelf life and yield are `numeric` (no decimals) — price ₹149.50 saved as ₹150, conversion 0.5 as 1 | [02](./02-add-or-edit.md) |
| D3 | **Ingredients can be wiped:** each save hard-deletes every ingredient line and re-inserts them with no transaction; a failure in between leaves the recipe empty | [02](./02-add-or-edit.md) |
| D4 | Each save switches off **every** `Stock` / `StockCheck` row of the recipe and re-enables only the chosen locations — a removed location's stock (even non-zero) disappears | [02](./02-add-or-edit.md) |
| D5 | **Loops:** a recipe can use itself, or A uses B while B uses A (the ingredient search even offers the recipe being edited) | [02](./02-add-or-edit.md) |
| D6 | Ingredients, category, menu section and locations aren't checked to belong to the company or be active; finished items can be ingredients through the API | [02](./02-add-or-edit.md) |
| D7 | Details, ingredient list, delete and remove-line take only an id — readable / deletable across companies | 01–05 |
| D8 | Delete reuses `SP_DelStockItem`: no company, no in-use check — a recipe on the POS, in production or inside another recipe can be deleted | [05](./05-delete-unused.md) |
| D9 | Recipe **cost is typed by hand**; food-cost % in Live Costing depends on that number | [03](./03-recipe-cost.md) |
| D10 | No server permission checks (`create_recipe`, `edit_recipe`, `delete_recipe`); SuperAdmin tokens pass `[Authorize]`; raw SQL error text returned; no stale-save check | all |
| D11 | Ingredient search can return one item twice (several default vendors) and offers inactive / deleted items | [02](./02-add-or-edit.md) |
| D13 | Every save from this screen **wiped the POS item mapping** (`Item_Map_POS`): the screen sends no POS item and the save deleted the mapping before re-inserting nothing. Now an empty value leaves the mapping alone | [02](./02-add-or-edit.md) |
| D14 | Removing an ingredient line in the dialog deleted it on the server **at once** — Cancel didn't bring it back. Now removal only changes the form; the save switches off left-out lines | [02](./02-add-or-edit.md) |
| D12 | List has no total, no status, no category / menu-section filter; no Active / Inactive state; page not on the recipe | [01](./01-view-and-search.md), [`ui-ux.md`](./ui-ux.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | Cost is **calculated from the ingredients**, and a user can **override** it with a manual cost (flagged "Manual") — see [03](./03-recipe-cost.md) | 2026-10-30 |
| Q2 | Active / Inactive **switch** + **Delete only if never used** (same as Raw Material); "used" adds: mapped to a POS item, on orders / KOTs | 2026-10-30 |
| Q3 | Semi-finished recipes **may use other semi-finished recipes**; the server refuses a recipe that contains itself directly or through others. Finished items are never ingredients | 2026-10-30 |
| Q4 | **Server paging + total**, type tabs kept; search, category, menu section and status on the server | 2026-10-30 |
| Q5 | Name: required, up to 100 characters, master character set; unique across the company's items of every type (shared with Raw Material). The recipe unit is **locked once used** (same rule as Raw Material Q2) | 2026-10-30 (default — change before approval if needed) |
| Q6 | The recipe **type (Semi-Finished / Finished) is fixed once created**: read-only in edit mode, and the server refuses a change ("Recipe type can't be changed. Create a new recipe of the other type instead."). Changing it would break recipes that use it as an ingredient, POS / order history, the finished-only fields and the type-bound category | 2026-10-30 |



## Depends on

Raw Material (ingredients, last purchase cost), Category Master, Menu Section, Setting → Location, POS item mapping.
