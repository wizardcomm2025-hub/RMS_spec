# 01 — View, search and filter menu sections

## Summary

A company user opens Inventory → Menu Section and sees every section of their company, active first, then by name. They can search by name and filter by status. The page shows clear loading, empty, no-match and error states.

## Prerequisites

- **Backend.** `GetMenuSectionWithPagination` returns every section of the company, sorted, no server paging (see [`api-contract.md`](./api-contract.md#list-sections)).
- **Data.** `SP_GetMenuSectionWithPagination` without `OFFSET` / `FETCH`.
- **Depends on.** none.

## Actors & preconditions

- **Actor:** a logged-in company user who can open Menu Section.
- **Trigger:** opening the page, Refresh, or after a save or status change.

## Main flow

1. The user opens Inventory → Menu Section.
2. Header (breadcrumb "Inventory › Menu Section", title "Menu Section", About), filter bar and skeleton rows while loading.
3. The list shows Menu Section Name, Status (Active / Inactive, centred) and Actions (Edit, switch; right); active first, then by name.
4. Search narrows to names containing the text (trimmed, case-insensitive) from the first character; Status (All / Active / Inactive) combines with it; "Clear filters" resets both.
5. Footer "Showing X of Y menu sections". No pagination.

## Alternative flows

- **No sections yet.** "No menu sections yet. Add the groups cashiers see on the POS menu." with Add (with `create_menu_section`).
- **No match.** "No menu sections match your filters." with Clear filters.
- **Load error.** "We couldn't load your menu sections. Check your connection and try again." with Retry; toast "Failed to load menu sections".
- **Other companies.** Never appear.

## Acceptance criteria

1. Given CO-A has 12 sections (active and inactive), then all 12 are listed, active first then by name, none of CO-B's.
2. Given an inactive section, then the "Inactive" chip.
3. Given Search "star" and Status "Active", then only active sections containing "star", from the first letter.
4. Given no match / no sections / a load failure, then the matching state (with Retry for the error).
5. Given 12 sections, then no pagination and "Showing 12 of 12 menu sections".
6. Given a user without `create_menu_section`, then no Add button.

## Platform notes

### RMS API

- `POST /Setting/GetMenuSectionWithPagination` — `[CompanyAuthorize]`; `RecordPerPage` / `Pagenumber` / `MenuSectionName` ignored.

### Data — Tenant DB

- Reads `MenuSection` only.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Page: [`ui-ux.md`](./ui-ux.md#1-page-anatomy).

## Configuration

None.

## Out of scope

None.

## Open questions

None.
