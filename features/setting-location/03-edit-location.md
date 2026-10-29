# 03 — Edit a location

## Summary

A company user with `Location.Edit` opens an existing location (active or inactive), changes its details and saves. The system applies the same validation as Add, keeps the original owner and creator, records who changed it, and never touches the location's bins. A duplicate name is reported as an error, not as a silent success.

## Prerequisites

- **Backend.** `GetWareHouseDetailsById` with `[Authorize]`, a safe "Location not found" and `IsActive` in the response; `SaveWareHouseDetails` as in [02](./02-add-location.md) (see [`api-contract.md`](./api-contract.md)).
- **Data.** `GetWarehouseBinDetailsbyWarehouseId` (`IsDeleted = 0`, returns `IsActive`); `Sp_SaveWareHouseDetails` keeps `UserResponsible` and `CreatedBy` on update.
- **Depends on.** [02](./02-add-location.md) — shares the save contract and validation; build them together.

## Actors & preconditions

- **Actor:** a company user with `Location.Edit`.
- **Trigger:** the Edit icon on a row or phone card.
- **Preconditions:** the location belongs to the user's company and is not deleted.

## Main flow

1. The user clicks Edit; the dialog "Edit Location — <name>" opens and loads the location behind a loading overlay.
2. The form shows the stored name, type, employees, address and flags.
3. The user changes fields; the name is checked for duplicates as they type, excluding this location.
4. The user clicks "Save changes"; the button shows a spinner and the dialog cannot be closed.
5. The system checks that the location has not changed since it was loaded (`UpdatedAt` sent back by the form), validates as in [02](./02-add-location.md) and updates the location: `UpdatedBy` = caller, `UpdatedAt` = now; `CreatedBy` and `UserResponsible` stay as they were; `IsPOSOutlet = 1`.
6. The system replaces the employee mappings with the chosen employees.
7. The dialog closes, "Location updated" appears and the list refreshes.

## Alternative flows

- **Duplicate name.** Another non-deleted location of the company already has the trimmed name: "Location name already exists" inline and as a toast; the dialog stays open; nothing changes (D2).
- **Rename to the same name.** Only case or spaces change on the same location: allowed.
- **Inactive location.** Opens and saves like an active one; the Default checkbox is disabled with the hint "Activate the location to make it default".
- **Location not found.** The id is unknown, deleted or another company's, on load or on save: "Location not found"; nothing is shown or changed (LOC-Q9).
- **Assigned user no longer resolvable.** The user is not in the active staff list: no chip is shown, but the mapping is kept on save.
- **Discard / double click / server failure.** As in [02](./02-add-location.md).
- **Concurrent edits.** Someone else saved or switched the location after it was loaded, or the request carries no `UpdatedAt`: "This location was changed by someone else. Reload and try again." as a toast; the dialog stays open; nothing changes (LOC-Q7).
- **Already-mapped employees.** Employees already mapped to this location are accepted even if they are no longer active staff; newly added ones must be active staff of the company (LOC-Q6).
- **No permission.** A direct API call without `Location.Edit`: "You don't have permission to perform this action."; nothing changes (LOC-Q1).

## Acceptance criteria

1. Given location A, when the user edits and saves, then "Location updated" is shown and the list shows the new values.
2. Given location B and A's name typed into B, then "Location name already exists" is shown live and on save, and nothing changes (old AC-2, D2).
3. Given user X created A and user Y edits it, then `UserResponsible` and `CreatedBy` stay X and `UpdatedBy` is Y (old AC-5, D5).
4. Given A has bins, when A is saved, then its `BinWarehouse` rows are unchanged (old AC-4, D4).
5. Given a request to load a location without a token, then 401 (old AC-3, D3).
6. Given an unknown id, then `Status=false`, "Location not found", and no exception (old AC-3, D3).
7. Given an id of a CO-B location with a CO-A token, then "Location not found" and no CO-B data in the response.
8. Given a save, then other locations' POS flags are unchanged and this one is `IsPOSOutlet = 1` (old AC-1).
9. Given a user without `Location.Edit`, then the Edit icon is hidden, and a direct save returns "You don't have permission to perform this action." with nothing changed (old AC-11, LOC-Q1).
10. Given an inactive location, then it opens, saves, and Default is disabled with the hint.
11. Given admin X loads A and admin Y then saves A, when X saves, then X gets "This location was changed by someone else. Reload and try again." and Y's values remain (LOC-Q7).
12. Given a save with the `WarehouseId` of an unknown, deleted or other company's location, then "Location not found" and nothing is written (LOC-Q9).

## Platform notes

### RMS API

- `POST /Setting/GetWareHouseDetailsById` — `[Authorize]` re-enabled; not-found handled before reading row 0; returns `UpdatedAt` as the edit token.
- `POST /Setting/SaveWareHouseDetails` with the location's `WarehouseId` and `UpdatedAt`; server-side `Location.Edit` check; `Exist` → `Status=false`; stale or missing `UpdatedAt` → refused.
- `POST /Users/ValidateLocName` with `locationId` = this location.

### Data — Tenant DB

- Reads `Warehouse`, `UserWarehouseMapping`; updates `Warehouse`; replaces `UserWarehouseMapping` for this location; never touches `BinWarehouse`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Same dialog as Add in edit mode: [`ui-ux.md`](./ui-ux.md#3-dialog--form).

## Configuration

None beyond [02](./02-add-location.md).

## Out of scope

- Address column naming (UI line 1 → `AddressLine1`, UI line 2 → `AddressLine`) — unchanged, see [`data-model.md`](./data-model.md#address-mapping).

## Open questions

None open. LOC-Q1, Q6, Q7, Q9 resolved 2026-10-28 (see [README](./README.md#cross-cutting-open-questions)).
