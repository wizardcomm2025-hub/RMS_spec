# 03 — Edit a vehicle

## Summary

A company user with `edit_vehicle_master` opens an existing vehicle (active or inactive) and updates its Registration Number or Car Manufacturer Name. The system validates the inputs, ensures the new registration does not conflict with another vehicle in the company, verifies the edit token hasn't become stale, updates the record, and preserves its existing Active/Inactive status.

## Prerequisites

- **Backend.** `GetVehicleById` (company filter, returns `lastUpdatedOn` concurrency token); `AddEditVechile` supporting update with stale check (see [`api-contract.md`](./api-contract.md#save-vehicle)).
- **Data.** `SP_GetVehicleById` and `SP_AddEditVehicle` updated with tenant isolation and stale token check.
- **Depends on.** [02](./02-add-vehicle.md).

## Actors & preconditions

- **Actor:** a company user with `edit_vehicle_master`.
- **Trigger:** clicking the Edit icon on a row or mobile card.

## Main flow

1. The user clicks the Edit icon on a vehicle row.
2. The system loads the vehicle via `GetVehicleById` behind a loading overlay.
3. The dialog "Edit Vehicle — <regNo>" opens with existing registration number and manufacturer name filled in.
4. The user modifies the registration number and/or manufacturer name. Registration duplicate checking ignores this vehicle's own ID.
5. The user clicks "Save changes".
6. The system checks that the vehicle still exists, belongs to the caller's company, and has not been modified by someone else (`lastUpdatedOn`), then updates `RegNo`, `BrandName`, `LastUpdatedBy`, and `LastUpdatedOn` (UTC). `IsActive`, `CompanyId`, and `CreatedOn` remain untouched.
7. The dialog closes, the toast "Vehicle updated successfully" appears, and the list refreshes.

## Alternative flows

- **Duplicate / invalid inputs.** Handled with the same rules and exact messages as [02](./02-add-vehicle.md). Its own current registration number is not considered a duplicate.
- **Inactive vehicle.** Can be edited; retains its Inactive status upon save.
- **Not found.** Unknown GUID, deleted, or belongs to another company: on load or save returns "Vehicle not found"; the dialog does not open, nothing is changed, and the list refreshes.
- **Changed by someone else (stale token).** The vehicle was modified by another user between load and save, or `lastUpdatedOn` token is missing/mismatched: "This vehicle was changed by someone else. Reload and try again."; nothing is saved.
- **Unchanged values.** Submitting without modifying any field succeeds and produces "Vehicle updated successfully".
- **No permission (direct call).** "You don't have permission to perform this action."
- **Discard / cancellation.** Closing with dirty fields prompts "Discard changes?".

## Acceptance criteria

1. Given vehicle A ("MH12AB1234"), when registration is updated to "MH12XY9999", then "Vehicle updated successfully" appears and the list shows the new registration.
2. Given vehicle B's registration typed into vehicle A, then "Registration number already exists" appears and save is refused.
3. Given an inactive vehicle, when edited and saved, then it remains Inactive.
4. Given a save payload that sends `IsActive = false` for an active vehicle, then the vehicle details are updated and it remains Active (status switch is handled in [04](./04-switch-vehicle-on-off.md)).
5. Given a vehicle ID belonging to CO-B called with a CO-A token, on load or save, then "Vehicle not found" appears and nothing is returned or modified.
6. Given user 1 and user 2 open the same vehicle, and user 1 saves changes, when user 2 attempts to save, then "This vehicle was changed by someone else. Reload and try again." appears and user 1's changes are preserved.
7. Given a user without `edit_vehicle_master`, then the Edit icon is hidden and a direct API call returns "You don't have permission to perform this action."

## Platform notes

### RMS API

- `POST /Setting/GetVehicleById` — `[CompanyAuthorize]`, returns vehicle data with `lastUpdatedOn`.
- `POST /Setting/AddEditVechile` with existing `VehicleId` and `lastUpdatedOn` — `[CompanyAuthorize]`, `edit_vehicle_master` enforced on server.

### Data — Tenant DB

- Updates `dbo.VehicleMaster` filtered by `VehicleId` and `CompanyId`. Updates `RegNo`, `BrandName`, `LastUpdatedBy`, and `LastUpdatedOn = GETUTCDATE()`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Route: `/admin/vehicle/`.
- Edit modal title: "Edit Vehicle — <regNo>". Submit button label: "Save changes".
