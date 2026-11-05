# Inventory → Raw Material

A **raw material** is something the company buys and stocks to cook with (tomato, oil, flour). It is a `dbo.StockItem` row of the company's **Raw** inventory type (`InventoryType.SystemName = 'Raw_Item'`, `ClassificationId = 1`). Each one has a category, an optional brand and sub-brand, a main unit and an alternate unit with a conversion, cost and price, reorder and minimum-order levels, shelf life and yield, the **locations** that use it (`ItemWarehouseMap`) and its **vendors** (`DistributorStockItemMapping`).

Raw materials are used by recipes, purchase orders, PO receive, requisitions, transfers, stock, wastage and production. The Purchase Order OCR screen opens the same Add dialog.

Status (2026-10-30): **Built** — approved 2026-10-30; DBSCRIPT files not yet run on dev; manual tests deferred. Plan: [`RMS_API/specs/raw-material/plan.md`](../../../RMS_API/specs/raw-material/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `RAW`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search.md`](./01-view-and-search.md) | See raw materials page by page; search; filter by category and status |
| 02 | [`02-add-or-edit.md`](./02-add-or-edit.md) | Add or edit a raw material (details, units, locations, vendors) |
| 03 | [`03-switch-on-off.md`](./03-switch-on-off.md) | Deactivate / reactivate a raw material |
| 04 | [`04-delete-unused.md`](./04-delete-unused.md) | Delete a raw material that has never been used |
| 05 | [`05-last-purchase-cost.md`](./05-last-purchase-cost.md) | Show the last purchase cost (pre-tax, per main unit) — built 2026-10-30 |

## Defects this feature fixes (found 2026-10-30)

| # | Defect | Fixed by |
|---|---|---|
| D1 | **Cross-company overwrite:** the edit runs `UPDATE StockItem … WHERE StockItemId = @id` and sets `CompanyId` to the caller's — any company can take over another company's item | [02](./02-add-or-edit.md) |
| D2 | Details, locations, vendors and delete take only the item id — readable / deletable across companies | 01–04 |
| D3 | **Decimals lost:** the save procedure declares cost, price, reorder, minimum order, shelf life and yield as `numeric` (no decimals); ₹12.50 is saved as ₹13 | [02](./02-add-or-edit.md) |
| D4 | Changing an item's default vendor sets `IsDefault` on **every** item of that vendor (`WHERE DistributorId = @VendorId` only) | [02](./02-add-or-edit.md) |
| D5 | Adding a location on edit inserts opening `Stock` / `StockCheck` rows with a new random id instead of the item's id (orphan stock rows) | [02](./02-add-or-edit.md) |
| D6 | Removing a vendor in the dialog is never saved (the remove only changes the screen) | [02](./02-add-or-edit.md) |
| D7 | Reference No, SKU and Remarks typed on the form are sent as empty and never saved | [02](./02-add-or-edit.md) |
| D8 | The screen sends inventory type `52` (hard-coded) for the name check; category, brand, sub-brand, locations and vendors aren't checked to belong to the company | [02](./02-add-or-edit.md) |
| D9 | No server permission checks (`create_raw_material`, `edit_raw_material`, `delete_raw_material`); SuperAdmin tokens pass `[Authorize]`; raw SQL error text returned; no transaction, no stale-save check | all |
| D10 | Changing the main unit of an item with stock or purchases silently makes old quantities wrong | [02](./02-add-or-edit.md) |
| D11 | List has no total (paging guesses), no status, no category filter; delete is hidden; page not on the recipe | [01](./01-view-and-search.md), [`ui-ux.md`](./ui-ux.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | Active / Inactive **switch** like the other masters, plus **Delete only if never used** (no recipe line, PO, PO receive, requisition, transfer, wastage, production, consumption, or stock other than zero) | 2026-10-30 |
| Q2 | **Main unit locked once used** (any of the uses in Q1); alternate unit and conversion stay editable | 2026-10-30 |
| Q3 | Name unique across **the whole company** (all inventory types), as today; the message names the type that has it | 2026-10-30 |
| Q4 | **Server paging + total**: search, category and status on the server; footer "1–25 of N raw materials" with page buttons (same as User List) | 2026-10-30 |
| Q5 | Name: required, up to 100 characters, letters (any language), digits, space and `& ( ) ' / . , -` — the same rule as the other masters (today: letters only, 50). Cost / price keep 2 decimals; quantities keep 4 | 2026-10-30 (default — change before approval if needed) |
| Q6 | Inactive raw materials disappear from the pickers on other screens (recipe, PO, requisition…) — they already show active items only — and stay on existing documents | 2026-10-30 |

## Found, not fixed here

- `/Setting/GetStockItemList` has **no login check** and takes `CompanyId` from the body (lists any company's items). It is used by the OCR flow, not by this screen — fix it with the Purchase Order / OCR module.

## Depends on

Category Master (Raw-type categories), Brands / Sub Brands, Setting → Location, Vendor. Recipe and PO OCR reuse this dialog.
