# 01 — View, search and filter departments

## Summary

A company user opens Setting → Department and sees every department of their company that isn't deleted, active first, then by name. They can search by name and filter by status. The page shows clear loading, empty, no-match and error states. The same complete list feeds the Roster screen's department filter.

## Prerequisites

- **Backend.** `GetDepartmentList` returns every non-deleted department of the company, sorted, with no server paging (see [`api-contract.md`](./api-contract.md#list-departments)).
- **Data.** `SP_GetAllDepartment` without `OFFSET` / `FETCH`, ordered active first then name (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** none.

## Actors & preconditions

- **Actor:** a logged-in company user who can open the Department menu.
- **Trigger:** opening the page, Refresh, or returning after a save or status change.

## Main flow

1. The user opens Setting → Department.
2. The page shows the header (breadcrumb "Setting › Department", title "Department", description, About), the filter bar and skeleton rows while loading.
3. The system loads all of the company's non-deleted departments.
4. The list shows Department Name, Status (Active / Inactive) and Actions (Edit, switch); active first, then by name.
5. The user types in Search; the list narrows to names containing the text (trimmed, case-insensitive) from the first character.
6. The user picks a Status (All / Active / Inactive); it combines with the search. "Clear filters" resets both.
7. The footer shows "Showing X of Y departments". No pagination.

## Alternative flows

- **No departments yet.** "No departments yet. Add your first department to group your staff." with an Add button (with `create_department`).
- **No match.** "No departments match your filters." with Clear filters.
- **Load error.** Inline "We couldn't load your departments. Check your connection and try again." with Retry, and the toast "Failed to load departments".
- **Refresh.** Thin progress bar on the card; rows stay.
- **Other companies, deleted rows.** Never appear.

## Acceptance criteria

1. Given CO-A has 12 departments (active and inactive), when A-ADMIN opens the page, then all 12 are listed, active first then by name, and none of CO-B's (D1).
2. Given an inactive department, then it shows the "Inactive" chip.
3. Given Search "kit" and Status "Active", then only active departments whose name contains "kit" are shown.
4. Given filters match nothing, then "No departments match your filters." and Clear filters; clicking it restores the list.
5. Given no departments, then the empty state with (permission permitting) an Add button.
6. Given the list call fails, then the inline error with Retry and the toast "Failed to load departments".
7. Given 12 departments, then there are no pagination controls and the footer reads "Showing 12 of 12 departments".
8. Given a user without `create_department`, then the Add button is hidden.
9. Given CO-A has 12 departments, when the Roster screen opens, then its department filter offers all 12 (D1).
10. The page meets the UI acceptance criteria in [`ui-ux.md`](./ui-ux.md#ui-acceptance-criteria).

## Platform notes

### RMS API

- `POST /AssetManagement/GetDepartmentList` — `[CompanyAuthorize]`; company from the token; `PageNumber` / `PageSize` in the body are ignored. Also used by Roster.

### Data — Tenant DB

- Reads `DepartmentMaster` (`CompanyId` = token company, `IsDelete = 0`).

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Route `admin/(setting)/department`; tab title "Department - RMS". Search and filter are client-side. Layout: [`ui-ux.md`](./ui-ux.md).

## Configuration

None.

## Out of scope

- Showing user counts per department — future.

## Open questions

None.
