# 04 — Switch a location on or off

## Summary

Instead of deleting a location, a user with `Location.Edit` switches it Inactive. An inactive location disappears from stock, requisition, POS and user screens but stays on the Location page, keeps its employee mappings and can be switched back on at any time. Deactivating the default location also removes its Default flag.

## Prerequisites

- **Backend.** New `POST /Setting/ChangeWareHouseStatus` (see [`api-contract.md`](./api-contract.md#change-status)).
- **Data.** New `Sp_ChangeWareHouseStatus` (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** [01](./01-view-and-filter-locations.md) — the list must show inactive rows (LOC-Q5).

## Actors & preconditions

- **Actor:** a company user with `Location.Edit`.
- **Trigger:** the Active switch on a row or phone card.
- **Preconditions:** the location belongs to the user's company and is not deleted.

## Main flow

1. The user turns the switch off on an active location.
2. A confirmation opens: "Deactivate **<name>**? It will be hidden from stock, requisition, POS and user screens. You can activate it again at any time." with Cancel and Deactivate.
3. The user clicks Deactivate.
4. The system sets `IsActive = 0` and `IsDefault = 0`, keeps `IsDeleted = 0`, and records `UpdatedBy` / `UpdatedAt`.
5. The toast "Location deactivated" appears and the row shows the "Inactive" chip.
6. To reactivate, the user turns the switch on, confirms "Activate **<name>**?" with Activate, and sees "Location activated"; the location is Active with its employees as before and Default off.

## Alternative flows

- **Cancel.** Nothing changes; the switch returns to its previous position.
- **Default location deactivated.** It loses Default; the company may have no default (allowed).
- **Not found.** Unknown, deleted or another company's id: "Location not found"; nothing changes.
- **Already in that state.** Switching to the state it already has changes nothing harmful (idempotent).
- **No delete.** No delete action exists anywhere in the UI (Q2). A direct call to the legacy delete endpoint returns "Locations cannot be deleted. Deactivate the location instead." and writes nothing (LOC-Q8).
- **No permission.** A direct call without `Location.Edit`: "You don't have permission to perform this action."; nothing changes (LOC-Q1).
- **Server failure.** Toast with the message; the switch returns to its previous position.

## Acceptance criteria

1. Given A is active, when it is switched off and confirmed, then it shows "Inactive" in this list and stores `IsActive = 0`, `IsDeleted = 0`, `IsDefault = 0` (old AC-9).
2. Given A is inactive, then it is not offered in any `IsActive = 1` dropdown, for example the requisition location list (old AC-9).
3. Given A is inactive, when it is switched on, then it is Active with the same employee mappings as before and Default off (old AC-10).
4. Given the user cancels the confirmation, then nothing changes.
5. Given a user without `Location.Edit`, then the switch is hidden, and a direct status call returns "You don't have permission to perform this action." with nothing changed (old AC-11, LOC-Q1).
6. Given an id of another company's location, then "Location not found" and that location is unchanged.
7. Given two status calls for the same location sent at once, then the final state equals the last call and no error leaks.
8. Given any caller, when `DeleteWareHouseDetails` is called for any location, then "Locations cannot be deleted. Deactivate the location instead." and no row changes (LOC-Q8).

## Platform notes

### RMS API

- `POST /Setting/ChangeWareHouseStatus` `{ warehouseId, isActive }`, `[Authorize]`, server-side `Location.Edit` check, company and user from the token. Updates `UpdatedAt`, so an edit form opened before the switch is refused as stale ([03](./03-edit-location.md)).
- `POST /Setting/DeleteWareHouseDetails` is blocked (LOC-Q8).

### Data — Tenant DB

- Updates `Warehouse` only. `UserWarehouseMapping` and `BinWarehouse` are untouched. Over 800 SP references filter `Warehouse` on `IsActive = 1`, so inactive locations disappear from other screens without changes there.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Switch, chips and confirm dialogs: [`ui-ux.md`](./ui-ux.md#5-confirmations).

## Configuration

None.

## Out of scope

- Screens that read `Warehouse` without an `IsActive` filter (plan R2), for example the profile location list — observed, not changed.

## Open questions

None open. LOC-Q1, Q8 resolved 2026-10-28 (see [README](./README.md#cross-cutting-open-questions)).
