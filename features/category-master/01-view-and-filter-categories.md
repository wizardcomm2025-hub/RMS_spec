# 01 — View, search and filter categories

## Summary

A company user opens Setting → Category Master and sees the company's item categories as a two-level list: main categories with their sub-categories underneath. They can search by name or code and filter by inventory type and status. The page shows clear loading, empty, no-match and error states. It is the entry point for every other Category use case.

## Prerequisites

- **Backend.** `GetStockGroupList` returns the company's non-deleted item categories (`ClassificationId = 1`) with `IsActive` (see [`api-contract.md`](./api-contract.md#list-categories)). `GetInventoryTypeCompanyWise` returns the company's active inventory types.
- **Data.** `SP_GetStockGroupList` filters `IsDeleted = 0` instead of `IsActive = 1` and returns `IsActive` (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** none — 02–04 return to this list.

## Actors & preconditions

- **Actor:** a logged-in company user who can open the Category Master menu.
- **Trigger:** opening the page, pressing Refresh, or returning after a save or status change.
- **Preconditions:** a valid session on the company's own host.

## Main flow

1. The user opens Setting → Category Master.
2. The page shows the header (breadcrumb "Setting › Category Master", title "Category Master", description and About), the filter bar and skeleton rows while loading.
3. The system loads the company's non-deleted item categories and its inventory types.
4. The list shows main categories, each with a "N sub" chip when it has sub-categories and an expand control. Expanding shows its sub-categories underneath. Columns: Category Name, Code, Parent, Inventory Type, Status, Actions.
5. Order: active before inactive, then by name — for main categories, and for sub-categories within their parent.
6. The user types in Search; the list shows categories whose name or code contains the text (trimmed, case-insensitive). A matching **main** category is shown expanded with all its sub-categories (that pass the type and status filters). A matching **sub-category** is shown under its parent, which appears for context with only the matching sub-categories.
7. The user picks an Inventory Type (All or one type) and / or a Status (All / Active / Inactive); filters combine with the search.
8. "Clear filters" appears when any filter is set and resets them all.
9. The footer shows "Showing X of Y categories". There is **no pagination**: the API returns the whole list, so every category is on one page (decided 2026-10-28).

## Alternative flows

- **No categories yet.** "No categories yet. Add your first category to organise your items." with an Add button (if the user has `create_item_category`).
- **No match.** "No categories match your filters." with Clear filters.
- **Load error.** Inline error with Retry and the toast "Failed to load categories". The page does not crash.
- **Types fail to load.** Toast "Failed to load inventory types"; the list still shows; the type filter offers only "All".
- **Refresh.** A thin progress bar on the card; rows stay visible.
- **Orphan sub-category.** A sub-category whose parent is not in the list (legacy data) is shown as a main-level row with its parent column "—".
- **Asset categories and other companies.** Never appear (`ClassificationId = 2` rows, other companies' rows).

## Acceptance criteria

1. Given CO-A has active and inactive categories, when A-ADMIN opens the page, then every non-deleted CO-A item category is listed in two levels, active first then by name, and no CO-B or asset category appears.
2. Given an inactive category, then it shows the "Inactive" status chip.
3. Given Search "veg", Inventory Type "Raw Material" and Status "Active", then only active Raw Material categories whose name or code contains "veg" are shown; a matching main category is expanded with all its active sub-categories.
4. Given filters match nothing, then "No categories match your filters." and Clear filters are shown; Clear filters restores the full list.
5. Given the company has no item categories, then the empty state text and (with permission) an Add button are shown.
6. Given the list call fails, then the inline error with Retry and the toast "Failed to load categories" are shown, and Retry reloads.
7. Given a main category with sub-categories, then it shows "N sub" and the sub-categories appear only when expanded (or when the search matches them).
8. Given more than 25 categories, then all of them are on one page with no pagination controls, and the footer shows "Showing X of Y categories".
9. Given a user without `create_item_category`, then the Add button is hidden.
10. The page meets the UI acceptance criteria in [`ui-ux.md`](./ui-ux.md#ui-acceptance-criteria).
11. Given Search matches a main category's name or code, then that main category is shown expanded with its sub-categories, even when their own names don't match (bug fixed 2026-10-28).

## Platform notes

### RMS API

- `POST /Setting/GetStockGroupList` — `[CompanyAuthorize]`; company from the token; item categories only. See [`api-contract.md`](./api-contract.md#list-categories). Only this screen calls it.
- `POST /Setting/GetInventoryTypeCompanyWise` — `[CompanyAuthorize]`; unchanged otherwise (also used by Wastage, Inbound Requisition, Recipe).

### Data — Tenant DB

- Reads `StockGroup` (`CompanyId` = token company, `ClassificationId = 1`, `IsDeleted = 0`) and `InventoryType`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Route `admin/(setting)/stock-group` (unchanged); browser tab title "Category Master - RMS". Search and filters are client-side. Layout and states: [`ui-ux.md`](./ui-ux.md).

## Configuration

- Inventory types per company (`InventoryType`). Page sizes are UI constants.

## Out of scope

- Server-side search and pagination — future.

## Open questions

None.
