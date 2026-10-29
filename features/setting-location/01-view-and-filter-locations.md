# 01 — View, search and filter locations

## Summary

A company user opens Setting → Location and sees every location of their company that is not deleted, active ones first, then by name. They can search by name and filter by type and status to find a location quickly. The page shows clear states for loading, empty, no match and errors, so the user always knows what is happening. This is the entry point for every other Location use case.

## Prerequisites

- **Backend.** `GetWareHouseDetails` with `IncludeInactive = true` returns active and inactive locations with `IsActive` (see [`api-contract.md`](./api-contract.md#list-locations)). `GetAllLocationDDl` returns the standard failure response.
- **Data.** `Sp_GetWareHouseandBinDetails` takes `@IncludeInactive`: when 1 it filters `IsDeleted = 0`, otherwise `IsActive = 1` as today; it returns `IsActive` (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** none — 02–05 return to this list.

## Actors & preconditions

- **Actor:** a logged-in company user who can open the Location menu.
- **Trigger:** opening Setting → Location, pressing Refresh, or returning after a save.
- **Preconditions:** a valid session on the company's own host.

## Main flow

1. The user opens Setting → Location.
2. The page shows the header (breadcrumb "Setting › Location", title "Location", description and About), the filter bar and skeleton rows while loading.
3. The system loads the company's non-deleted locations and the company's location types.
4. The list shows each location with Name (and a "Default" badge when set), Type, Address, Requisition (Yes / No), Status (Active / Inactive) and actions. Active locations come first, then by name.
5. The user types in Search; the list narrows to names containing the text (trimmed, case-insensitive).
6. The user picks a Type and / or a Status (All / Active / Inactive, default All); filters combine with the search.
7. "Clear filters" appears when any filter is set and resets them all.
8. The footer shows "Showing X of Y"; with more than 25 rows, pagination appears (10 / 25 / 50 per page).

## Alternative flows

- **No locations yet.** The company has none: the empty state with an Add button (if the user has `Location.Create`).
- **No match.** Filters hide every row: "No locations match your filters." with Clear filters.
- **Load error.** The list call fails: an inline error with Retry and an error toast "Failed to load locations". The page does not crash.
- **Types fail to load.** Toast "Failed to load location types"; the list still shows.
- **Refresh.** A thin progress bar runs on the card while rows stay visible.
- **Another company's locations.** Never appear, whatever the user does (company comes from the token).
- **Deleted rows.** Locations with `IsDeleted = 1` never appear.

## Acceptance criteria

1. Given CO-A has active and inactive locations, when A-ADMIN opens the page, then every non-deleted CO-A location is listed, active first then by name, and no CO-B location appears.
2. Given an inactive location, when the list loads, then it shows with the "Inactive" status chip.
3. Given the Status filter is "Inactive", a type is selected and a search text is typed, then only rows matching all three are shown (old AC-12).
4. Given filters match nothing, then "No locations match your filters." and Clear filters are shown; clicking it restores the full list.
5. Given the company has no locations, then the empty state "No locations yet. Add your first outlet, warehouse or kitchen." is shown.
6. Given the list call fails, then an inline error with Retry and the toast "Failed to load locations" are shown, and Retry reloads (old AC-13).
7. Given `GetAllLocationDDl` or the list call fails unexpectedly, then the API returns `Status=false`, "Something went wrong. Please try again." — never the bare string `"Error"` or exception text (old AC-13, D8, LOC-Q2).
8. Given more than 25 locations, then pagination with 10 / 25 / 50 per page appears and the footer shows "Showing X of Y".
9. Given a user without `Location.Create`, then the Add Location button is hidden (old AC-11).
10. The page meets the UI acceptance criteria in [`ui-ux.md`](./ui-ux.md#ui-acceptance-criteria) (responsive, theme tokens, keyboard).
11. Given a call without `IncludeInactive` (the other screens), then only active locations are returned (LOC-Q5).

## Platform notes

### RMS API

- `GetWareHouseDetails` `[Authorize]`; company from the token; the Location screen sends `IncludeInactive: true`. The 8 other callers send nothing and keep receiving active locations only (plan R8, LOC-Q5). A SuperAdmin token is rejected with 401 (LOC-Q11). See [`api-contract.md`](./api-contract.md#list-locations).
- `GetAllLocationDDl` `[Authorize]`; on failure returns `Status=false`, "Something went wrong. Please try again." (LOC-Q2).

### Data — Tenant DB

- Reads `Warehouse` (`CompanyId` = token company, `IsDeleted = 0`) and `Map_Company_LocationType`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Route `admin/(setting)/warehouse`; page title "Location - RMS".
- Search and filters are client-side. Layout, states and sizes: [`ui-ux.md`](./ui-ux.md).

## Configuration

- Location types per company (`Map_Company_LocationType`).
- Page sizes (10 / 25 / 50) and the 25-row threshold are UI constants.

## Out of scope

- Server-side search and pagination — future.
- Export.

## Open questions

None open. LOC-Q2, Q5, Q11 resolved 2026-10-28 (see [README](./README.md#cross-cutting-open-questions)).
