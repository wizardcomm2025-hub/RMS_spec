# 01 — View, search and filter vehicles

## Summary

A company user opens Setting → Vehicle Master and sees every vehicle of their company that isn't deleted, active first, then by registration number. They can search across both Registration Number and Car Manufacturer Name, and filter by status (All / Active / Inactive). The page shows clear loading, empty, no-match, and error states.

## Prerequisites

- **Backend.** `GetVechileList` returns every non-deleted vehicle of the caller's company, sorted active first then by registration number, with company isolation (see [`api-contract.md`](./api-contract.md#list-vehicles)).
- **Data.** `SP_GetVechileList` scoped strictly to `@CompanyId` (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** none.

## Actors & preconditions

- **Actor:** a logged-in company user who can open the Vehicle Master menu.
- **Trigger:** opening the page, clicking Refresh, or returning after an add, edit, or status toggle.

## Main flow

1. The user opens Setting → Vehicle Master.
2. The page displays the header (breadcrumb "Setting › Vehicle Master", title "Vehicle Master", About button, Refresh icon, and Add Vehicle button if permitted), the filter bar, and skeleton rows while loading.
3. The system loads all of the company's vehicles.
4. The list displays Registration Number, Car Manufacturer Name, Status (Active / Inactive chip), and Actions (Edit icon, Active switch); active first, then by Registration Number.
5. The user types in Search; the list narrows in real-time to vehicles where Registration Number OR Car Manufacturer Name contains the typed text (trimmed, case-insensitive).
6. The user selects a Status (All / Active / Inactive); the filter combines with the search text. "Clear filters" resets both.
7. The footer displays "Showing X of Y vehicles". No server pagination is used.

## Alternative flows

- **No vehicles yet.** "No vehicles yet. Add your first vehicle for deliveries and stock transfers." with an Add button (if user has `create_vehicle_master`).
- **No match.** "No vehicles match your filters." with a Clear filters button.
- **Load error.** Inline "We couldn't load your vehicles. Check your connection and try again." with Retry button, and toast "Failed to load vehicles".
- **Refresh.** Thin progress bar on the card; rows stay visible while reloading.
- **Other companies' vehicles.** Never appear in the list.

## Acceptance criteria

1. Given CO-A has 10 vehicles (active and inactive), when A-ADMIN opens the page, then all 10 are listed, active first then by registration number, and no vehicles belonging to CO-B are visible.
2. Given an inactive vehicle, then it displays the "Inactive" chip.
3. Given Search "MH12" and Status "Active", then only active vehicles containing "MH12" in registration number or manufacturer are displayed.
4. Given Search "Toyota", then vehicles with manufacturer containing "Toyota" are displayed even if registration number does not match (D5).
5. Given filters match nothing, then "No vehicles match your filters." appears with a "Clear filters" button that restores the list when clicked.
6. Given a company with no vehicles, then the empty state is displayed with an "Add Vehicle" button.
7. Given the list API fails, then the inline error message with Retry button and the toast "Failed to load vehicles" appear.
8. Given 10 vehicles, then there are no pagination controls and the footer displays "Showing 10 of 10 vehicles".
9. Given a user without `create_vehicle_master`, then the Add Vehicle button is hidden.
10. The header displays breadcrumb, title, and About panel, omitting the duplicate short description.

## Platform notes

### RMS API

- `POST /Setting/GetVechileList` — `[CompanyAuthorize]`; `CompanyId` extracted from the token; body may be empty.

### Data — Tenant DB

- Reads `dbo.VehicleMaster` (`CompanyId` = token company). Ordered by `IsActive DESC, RegNo ASC`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Route: `/admin/vehicle/` (friendly rewrite `/setting/vehicle_master`).
- Permissions: `PERMISSIONS.VehicleMaster.Create`, `PERMISSIONS.VehicleMaster.Edit`.

## Configuration

What is configurable: Role permissions (`create_vehicle_master`, `edit_vehicle_master`).

## Out of scope

- Server-side pagination.
- Vehicle-specific document uploads.

## Open questions

None.
