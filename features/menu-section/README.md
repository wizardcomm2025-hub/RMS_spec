# Inventory → Menu Section

A **Menu Section** is a group on the POS menu that cashiers and guests see — Starters, Main Course, Beverages. Each finished item (a recipe) is put in one section (`StockItem.MenuSectionId`), and the POS lists items by section. Each company keeps its own list in the Tenant DB table `dbo.MenuSection`; new companies get a starter set at onboarding (copied from `Meta_MenuSection`, linked by `Meta_MenuSectionId`).

The screen lets a company admin list and search sections, add and rename them, and switch a section Active or Inactive. Sections are never deleted.

Status (2026-10-29): **Approved — API and UI implemented (builds pass); DBSCRIPT procedures changed, not yet run on dev; not tested.** Plan: [`RMS_API/specs/menu-section/plan.md`](../../../RMS_API/specs/menu-section/plan.md).

Feature code (used in test IDs): `MSE`

## Scope

- Fix the defects below (company isolation first).
- Rebuild the page on the list recipe (`UI_UX_STANDARDS.md` §12) with a module dialog like Department's.
- Recipe dialog: link the section by id and keep a switched-off section on existing recipes.
- Server-side rules and the API patterns used by Department.

## Out of scope

- Ordering sections on the POS (no sort column).
- Section images, colours or time windows.
- The POS app's own screens and `Sp_GetProductsData*` (they filter by the section ids the POS sends).
- `Meta_MenuSection` and onboarding.
- The rest of the Recipe screen.

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search-menu-sections.md`](./01-view-and-search-menu-sections.md) | See every section of the company; search; filter by status |
| 02 | [`02-add-menu-section.md`](./02-add-menu-section.md) | Create an active section |
| 03 | [`03-rename-menu-section.md`](./03-rename-menu-section.md) | Change a section's name |
| 04 | [`04-switch-menu-section-on-off.md`](./04-switch-menu-section-on-off.md) | Deactivate or reactivate a section instead of deleting it |

## Key concepts

Menu Section — see the [glossary](../../project-overview.md#glossary).

## Data, API, UI and tests

- Data model: [`data-model.md`](./data-model.md)
- API contract: [`api-contract.md`](./api-contract.md)
- UI / UX: [`ui-ux.md`](./ui-ux.md)
- Test cases: [`test-cases/README.md`](./test-cases/README.md)
- Plan, tasks, test results: `RMS_API/specs/menu-section/`
- Module explainer: `RMS_API/specs/MODULES.md` → Menu Section

## Defects this feature fixes (found 2026-10-29)

| # | Defect | Fixed by |
|---|---|---|
| D1 | **Cross-company overwrite:** `SP_AddEditMenuSection` updates `WHERE MenuSectionId = @MenuSectionId` only — any company can rename or switch off another company's section by id | [03](./03-rename-menu-section.md) |
| D2 | **Cross-company read / switch-off:** `SP_GetMenuSectionById` and `SP_DeleteMenuSection` take no company — any company can read, or switch off ("delete" sets `IsActive = 0`), another company's section | [03](./03-rename-menu-section.md), [04](./04-switch-menu-section-on-off.md) |
| D3 | A duplicate save returns `Allready_Exit` with `status: true`; the UI ignores the result and shows "Menu Section added successfully" while nothing is saved | [02](./02-add-menu-section.md) |
| D4 | The duplicate check is untrimmed and only looks at **active** sections: "Starters" can be added next to an inactive "Starters" | [02](./02-add-menu-section.md) |
| D5 | Get-by-id returns only **active** rows, so editing an inactive section silently falls back to the list row; the edit save can change `IsActive` | [03](./03-rename-menu-section.md) |
| D6 | The status switch saves through the full add/edit call with no confirmation and no check — a section with live recipes can be switched off and its items drop out of Recipe's dropdown | [04](./04-switch-menu-section-on-off.md) |
| D7 | **Recipe** links the section by matching its *name*: editing a recipe whose section is inactive or renamed saves it with **no** section | [04](./04-switch-menu-section-on-off.md) |
| D8 | No server-side permission checks; SuperAdmin tokens pass `[Authorize]`; exception text returned; `GETDATE()` instead of UTC; no concurrency guard | all |
| D9 | Page not on the list recipe: full-page loader, client pagination, search only from 3 characters (server-side), no load-error / empty / no-match states, no phone layout; tab "Menu Section - Admin Dashboard"; dead `customer-row.tsx` (template leftover) | [`ui-ux.md`](./ui-ux.md) |

## Depends on

- **User Management → Roles** — `create_menu_section` / `edit_menu_section`.
- **Inventory → Recipe** — consumer (active list + the recipe's current section).
- **Setting → Department** — the shared `[CompanyAuthorize]`, `IPermissionService` and stale-save pattern.

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | A section can't be switched off while **active recipes** use it (`StockItem.MenuSectionId`, `IsActive = 1`, not deleted) | 2026-10-29 |
| Q2 | Name: same as Department — letters, numbers, spaces and `& - ( ) ' / . ,`, trimmed, 1–100; unique per company among active and inactive sections, case-insensitive | 2026-10-29 |
| Q3 | No delete; the API refuses it and the UI has none | 2026-10-29 |
| Q4 | Recipe links by id; an existing recipe keeps a switched-off section (shown "<name> (inactive)"); only active sections are offered as a new choice | 2026-10-29 |
| Q5 | Keep today's About text | 2026-10-29 |
| Q6 | Full list, client-side search and filter, no pagination; same API patterns as Department | 2026-10-29 |

## Cross-cutting open questions

None.
