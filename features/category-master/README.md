# Setting → Category Master

A **Category** groups inventory items so they can be found, counted and reported together, for example "Vegetables" under Raw Material. Each company has its own categories per **inventory type** (Raw Material, Semi-Finished, Finished Goods…), in two levels: **main categories** and **sub-categories** under a main category of the same type. In the database the entity is **StockGroup** (`dbo.StockGroup`, `ClassificationId = 1`); the screen was called "Item Category" and is renamed **Category Master**.

The screen lets a company admin list, search and filter categories, add and edit them (name, code, inventory type, parent), and switch a category Active or Inactive. Categories are never deleted.

Status (2026-10-28): **Approved — API and UI implemented (builds pass); SQL written, not yet deployed; not tested.** Plan: [`RMS_API/specs/category-master/plan.md`](../../../RMS_API/specs/category-master/plan.md).

Feature code (used in test IDs): `CAT`

## Scope

- Rename "Item Category" to **Category Master** everywhere a user sees it (menu, breadcrumb, title, dialog, buttons, toasts, browser tab), including the database menu name. The route `/admin/stock-group` and the permission system names (`create_item_category`, `edit_item_category`, …) stay.
- Fix the defects listed below.
- Replace delete with an Active / Inactive switch.
- Rebuild the page with the approved list-page recipe (`UI_UX_STANDARDS.md` §12, reference: Setting → Location).

## Out of scope

- Asset categories (`ClassificationId = 2`) in the same table — never read or changed by this screen; their screens are untouched.
- Inventory types themselves (they are company setup).
- Category images (`ImagePath`) and the intangible flag (`IsIntangible`) — not on the form; existing values are kept on edit.
- Re-assigning items to other categories (done on the item screens).
- Server-side pagination.

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-filter-categories.md`](./01-view-and-filter-categories.md) | See the company's categories as a two-level list; search and filter by type and status |
| 02 | [`02-add-category.md`](./02-add-category.md) | Create a main category or a sub-category |
| 03 | [`03-edit-category.md`](./03-edit-category.md) | Change name, code, type or parent of a category |
| 04 | [`04-switch-category-on-off.md`](./04-switch-category-on-off.md) | Deactivate or reactivate a category instead of deleting it |

## Key concepts

Category, Main category, Sub-category, Inventory type, Category code — see the [glossary](../../project-overview.md#glossary).

## Data, API, UI and tests

- Data model: [`data-model.md`](./data-model.md)
- API contract: [`api-contract.md`](./api-contract.md)
- UI / UX: [`ui-ux.md`](./ui-ux.md)
- Test cases: [`test-cases/README.md`](./test-cases/README.md)
- Plan, tasks, test results: `RMS_API/specs/category-master/` (`plan.md`, `tasks.md`, `test-results.md`)
- Module explainer: `RMS_API/specs/MODULES.md` → Category Master

## Defects this feature fixes (found 2026-10-28)

| # | Defect | Fixed by |
|---|---|---|
| D1 | `SP_AddEditStockGroup` edit is `UPDATE … WHERE StockGroupId = @id` with **no company filter**: a company can overwrite another company's category | [03](./03-edit-category.md) |
| D2 | `SP_GetStockGroupById` has **no company filter**: any category can be read by id | [03](./03-edit-category.md) |
| D3 | `SP_DelStockGroupById` deletes by id only (**no company filter**), ignores sub-categories and items, and returns the raw SQL error text | [04](./04-switch-category-on-off.md) |
| D4 | Duplicate save returns `status: true` with `Response = "Exist"`; the UI shows "created successfully" and nothing is saved | [02](./02-add-category.md), [03](./03-edit-category.md) |
| D5 | Names are not trimmed; the two duplicate checks disagree (one per classification, one not) | [02](./02-add-category.md) |
| D6 | The parent is not validated on the server: other type, itself, a sub-category, inactive or another company's category are accepted; a main category with children can become a child | [02](./02-add-category.md), [03](./03-edit-category.md) |
| D7 | Edit overwrites `ImagePath` with an empty string (the UI sends `null`) | [03](./03-edit-category.md) |
| D8 | No server-side permission checks; exception text returned to the caller; SuperAdmin tokens pass `[Authorize]` | all |
| D9 | Delete code exists in the page but the row has no Delete button | [04](./04-switch-category-on-off.md) |
| D10 | Page not on the list recipe: no `PageHeader`, full-page loader, no load-error / empty / no-match states, no phone layout, fixed `minWidth: 600`, **hex colours** in the type chips | [`ui-ux.md`](./ui-ux.md) |
| D11 | Mixed wording ("Item Category" / "Stock group"); the code field says "Auto-generated" but nothing generates it; tab title "Stock Group RMS" | [`ui-ux.md`](./ui-ux.md) |
| D12 | Menu Section's About link to this page never shows: its link key "Item Category" doesn't appear in the About text (the target `/setting/item_category` is a valid rewrite to `/admin/stock-group`) | [`ui-ux.md`](./ui-ux.md) |

## Depends on

- **User Management → Roles** — `create_item_category` / `edit_item_category` permissions.
- **Company setup** — the company's inventory types (`InventoryType`).
- **Setting → Location** — the shared `[CompanyAuthorize]` attribute, `IPermissionService.HasPermissionAsync` and the stale-save pattern introduced there.

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | No delete; Active / Inactive switch. A category can't be deactivated while it has active sub-categories or active items | 2026-10-28 |
| Q2 | Name: letters, numbers, spaces and `& - ( ) ' / . ,`, trimmed, 1–100 characters. Code: required, 1–10 uppercase letters or digits, suggested from the name, editable | 2026-10-28 |
| Q3 | Name and code are each unique per company **and inventory type**, among non-deleted categories (active and inactive), case-insensitive | 2026-10-28 |
| Q4 | Rename labels and the database menu name to "Category Master"; keep the route and permission system names | 2026-10-28 |
| Q5 | Two levels only. A sub-category's parent must be an active main category of the same type and company | 2026-10-28 |
| Q6 | Same API patterns as Location: `[CompanyAuthorize]`, server-side permissions, generic error message, `UpdatedAt` edit token, legacy delete blocked | 2026-10-28 |

## Cross-cutting open questions

- **CAT-Q1** Can a category's inventory type change after it has sub-categories or items? _Resolved 2026-10-28: no — "Inventory type can't be changed while the category has sub-categories or items."_
- **CAT-Q2** Existing data may already break the new rules (names with other characters or over 100, empty, lower-case or duplicate codes). _Resolved 2026-10-28: existing rows are shown as they are; the new rules apply when a category is saved, so editing such a row requires fixing it. A pre-check query lists them (plan step 0)._
- **CAT-Q3** Code suggestion: _Resolved 2026-10-28: the first three letters of each of the first two words of the name, upper-case, letters and digits only ("Fresh Vegetables" → "FREVEG"; "Dairy" → "DAI"). Suggested only while the user hasn't typed a code._
