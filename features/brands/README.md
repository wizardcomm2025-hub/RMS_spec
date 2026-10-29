# Inventory → Brand and Sub Brand

A **Brand** is the manufacturer name on what you buy (Amul, Fortune); a **Sub Brand** is a product line inside a brand (Thums Up under Coca-Cola). Both are optional on Raw Material (`StockItem.StockTypeId`, `StockItem.StockSubTypeId`). In the database a brand is a `StockType` and a sub-brand a `StockSubType` (Tenant DB, per company).

Two screens — Inventory → Brand and Inventory → Sub Brand — let a company admin list and search, add and edit, and switch Active / Inactive. Nothing is deleted.

Status (2026-10-29): **Approved — API and UI implemented (builds pass); DBSCRIPT procedures changed, not yet run on dev; not tested.** Plan: [`RMS_API/specs/brands/plan.md`](../../../RMS_API/specs/brands/plan.md).

Feature code (used in test IDs): `BRD`

## Scope

- Fix the defects below (anonymous legacy endpoints and company isolation first).
- Rebuild both pages on the list recipe (`UI_UX_STANDARDS.md` §12) with Department-style dialogs.
- Item and sub-brand counts come from the list query.
- Server-side rules and the API patterns used by Department.

## Out of scope

- `IsShortShelfLife` on a brand: kept as stored (never changed by these screens; nothing reads it).
- The Raw Material screen (it keeps using the active brand / sub-brand dropdowns).
- Merging or moving raw materials between brands.

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search-brands.md`](./01-view-and-search-brands.md) | See every brand with its sub-brands and item count; search; filter |
| 02 | [`02-add-or-rename-brand.md`](./02-add-or-rename-brand.md) | Add a brand or change its name |
| 03 | [`03-switch-brand-on-off.md`](./03-switch-brand-on-off.md) | Deactivate / reactivate a brand |
| 04 | [`04-view-and-search-sub-brands.md`](./04-view-and-search-sub-brands.md) | See every sub-brand with its brand and item count; search; filter by brand and status |
| 05 | [`05-add-or-edit-sub-brand.md`](./05-add-or-edit-sub-brand.md) | Add a sub-brand under a brand, rename it, or move it to another brand |
| 06 | [`06-switch-sub-brand-on-off.md`](./06-switch-sub-brand-on-off.md) | Deactivate / reactivate a sub-brand |

## Data, API, UI and tests

[`data-model.md`](./data-model.md) · [`api-contract.md`](./api-contract.md) · [`ui-ux.md`](./ui-ux.md) · [`test-cases/README.md`](./test-cases/README.md) · plan: `RMS_API/specs/brands/` · `MODULES.md` → Brand and Sub Brand

## Defects this feature fixes (found 2026-10-29)

| # | Defect | Fixed by |
|---|---|---|
| D1 | **Anonymous, any-company endpoints:** `AddEditStockType`, `AddEditStockSubType`, `GetStockTypeById`, `GetStockSubTypeById` have **no login check** and no company filter — anyone can read or rewrite any company's brands and sub-brands by id (the UI doesn't use them) | api-contract |
| D2 | **Cross-company overwrite:** `SP_AddEditStockTypeDetails` updates by id only; `SP_AddEditStockSubTypeDetails` updates by id only **and sets `CompanyId`**, moving another company's sub-brand into the caller's company; `SP_DeleteStockType` / `SP_DeleteStockSubType` switch off any company's row; get-by-id has no company filter | [02](./02-add-or-rename-brand.md), [05](./05-add-or-edit-sub-brand.md) |
| D3 | The Sub Brand list and get-by-id return **only active** sub-brands of **active** brands: a switched-off sub-brand (or every sub-brand of a switched-off brand) disappears from the page and can never be switched back on | [04](./04-view-and-search-sub-brands.md) |
| D4 | Saves always report success (no `Status=false`, duplicates return a text code); duplicate checks are untrimmed and only look at active rows; the edit save can flip `IsActive` and write `IsDeleted` | [02](./02-add-or-rename-brand.md), [05](./05-add-or-edit-sub-brand.md) |
| D5 | Switching a brand off leaves its active sub-brands and raw materials pointing at an inactive brand; a sub-brand can be moved to another brand while raw materials use it | [03](./03-switch-brand-on-off.md), [05](./05-add-or-edit-sub-brand.md) |
| D6 | The Sub Brand page counts items by loading **every raw material one by one** (up to 1 000 calls each time the page opens) | [04](./04-view-and-search-sub-brands.md) |
| D7 | No server-side permission checks; SuperAdmin tokens pass `[Authorize]`; exception text returned; `GETDATE()` instead of UTC; no concurrency guard | all |
| D8 | Pages not on the list recipe: full-page loader, client pagination, search from 3 characters, no load-error / empty / no-match states, no phone layout; `SingleFieldModal`; dead `brand-form.tsx` / `brand-edit.tsx` | [`ui-ux.md`](./ui-ux.md) |

## Depends on

User Management → Roles (`create_brand`, `edit_brand`, `create_sub_brand`, `edit_sub_brand`); Setting → Department (shared API patterns); Inventory → Raw Material (consumer).

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | A brand can't be switched off while it has **active sub-brands** or **active raw materials** | 2026-10-29 |
| Q2 | A sub-brand can't be switched off while **active raw materials** use it; it can't be switched on while its brand is off | 2026-10-29 |
| Q3 | Names: same as Department (trimmed, 1–100, letters, numbers, spaces and & - ( ) ' / . ,); brand unique in the company; sub-brand unique **within its brand**; switched-off rows count | 2026-10-29 |
| Q4 | A sub-brand can move to another (active) brand only while no active raw material uses it | 2026-10-29 |
| Q5 | No delete anywhere; the four legacy anonymous endpoints are refused | 2026-10-29 |
| Q6 | Full lists, client-side search and filters, no pagination; About texts kept | 2026-10-29 |

## Cross-cutting open questions

None.
