# 01 — View, search and filter reasons of wastage

## Summary

A company user opens Setting → Reason of Wastage and sees every reason of their company that isn't deleted, active first, then alphabetically. They can search by text and filter by status. The page shows clear loading, empty, no-match and error states. Wastage Entry asks the same API for **active** reasons only and no longer filters them by its item search (D2).

## Prerequisites

- **Backend.** `GetWarehouseReason` returns every non-deleted reason of the company (`OpenMode: "All"`) or only active ones (`OpenMode: "Active"`), sorted, with no server paging (see [`api-contract.md`](./api-contract.md#list-reasons)).
- **Data.** `SP_GetWarehouseReason` without `OFFSET` / `FETCH` (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** none.

## Actors & preconditions

- **Actor:** a logged-in company user who can open the Reason of Wastage menu.
- **Trigger:** opening the page, Refresh, or returning after a save or status change.

## Main flow

1. The user opens Setting → Reason of Wastage.
2. The page shows the header (breadcrumb "Setting › Reason of Wastage", title "Reason of Wastage", About), the filter bar and skeleton rows while loading.
3. The system loads all of the company's non-deleted reasons.
4. The list shows Reason, Status (Active / Inactive) and Actions (Edit, switch); active first, then by reason text.
5. The user types in Search; the list narrows to reasons containing the text (trimmed, case-insensitive) from the first character.
6. The user picks a Status (All / Active / Inactive); it combines with the search. "Clear filters" resets both.
7. The footer shows "Showing X of Y reasons". No pagination.

## Alternative flows

- **No reasons yet.** "No reasons yet. Add the reasons staff pick when they write off stock." with an Add button (with `create_reason_of_wastage`).
- **No match.** "No reasons match your filters." with Clear filters.
- **Load error.** Inline "We couldn't load your reasons. Check your connection and try again." with Retry, and the toast "Failed to load reasons".
- **Refresh.** Thin progress bar on the card; rows stay.
- **Long text.** Wraps in the table cell and the phone card; never widens the page.
- **Other companies, deleted rows.** Never appear.
- **Wastage Entry.** Loads `OpenMode: "Active"` once when the page opens and after a quick-add; the item search no longer reloads reasons (D2).

## Acceptance criteria

1. Given CO-A has 12 non-deleted reasons (active and inactive), when A-ADMIN opens the page, then all 12 are listed, active first then A–Z, and none of CO-B's or deleted ones (D8).
2. Given an inactive reason, then it shows the "Inactive" chip.
3. Given Search "exp" and Status "Active", then only active reasons containing "exp" are shown, from the first typed character.
4. Given filters match nothing, then the no-match state with Clear filters.
5. Given a company with no reasons, then the empty state; the Add button only with `create_reason_of_wastage`.
6. Given the list call fails, then the load-error state with Retry, and Retry reloads.
7. Given 12 reasons, then no pagination control and the footer "Showing 12 of 12 reasons".
8. Given a user without `create_reason_of_wastage`, then no Add button anywhere.
9. Given Wastage Entry, when the reason dropdown opens, then only active reasons are offered, and typing in the item search doesn't change the dropdown (D1, D2).
10. Given a 300-character reason, then it wraps without horizontal page scroll at 375 px and 1280 px.

## Platform notes

### RMS API

- `POST /Setting/GetWarehouseReason` — `[CompanyAuthorize]`; `OpenMode` `"Active"` / `"All"` (anything else = `"All"`); `SearchText` still honoured (trimmed); `PageNumber` / `PageSize` ignored.

### Data — Tenant DB

- Reads `WastageReasonMaster` only.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Page: [`ui-ux.md`](./ui-ux.md#1-page-anatomy). Wastage Entry: [`ui-ux.md`](./ui-ux.md#11-wastage-entry).

## Configuration

None.

## Out of scope

- Usage counts per reason.

## Open questions

None.
