# 04 — Switch a vehicle on or off

## Summary

Instead of hard-deleting a vehicle (which breaks audit trails and past stock transfer histories), a user with `edit_vehicle_master` switches the vehicle Inactive. An inactive vehicle is hidden from dispatch dropdowns in Inbound Requisition and Stock Transfer, but remains listed here and can be reactivated at any time. Direct deletion calls via API are refused.

## Prerequisites

- **Backend.** `POST /Setting/ChangeVehicleStatus` endpoint (see [`api-contract.md`](./api-contract.md#change-status)); `DeleteVehicle` permanently blocked.
- **Data.** `SP_ChangeVehicleStatus` stored procedure (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** [01](./01-view-and-search-vehicles.md).

## Actors & preconditions

- **Actor:** a company user with `edit_vehicle_master`.
- **Trigger:** clicking the Active switch on a row or mobile card.

## Main flow

1. The user turns off the Active switch on an active vehicle.
2. A confirmation dialog opens: title "Deactivate vehicle", text "Deactivate **<regNo>**? Staff won't be able to assign it to delivery or stock transfers. Past transfers keep this vehicle. You can activate it again at any time." — Cancel · **Deactivate**.
3. The user clicks Deactivate.
4. The system validates the vehicle belongs to the caller's company, sets `IsActive = 0`, and records `LastUpdatedBy` and `LastUpdatedOn` (UTC).
5. The toast "Vehicle deactivated" appears; the row displays the "Inactive" chip.
6. To reactivate: the user turns on the switch, confirms in the modal ("Activate **<regNo>**?"), clicks Activate, and the toast "Vehicle activated" appears.

## Alternative flows

- **Cancel.** Closing the confirmation modal or clicking Cancel leaves the switch and vehicle state unchanged.
- **Already in requested state.** Succeeds idempotently without error.
- **Not found.** Unknown, deleted, or another company's vehicle ID: returns "Vehicle not found"; no changes made.
- **Delete refused.** A direct API call to `DeleteVehicle` returns 200 with `status: false` and message "Vehicles cannot be deleted. Deactivate the vehicle instead."; nothing is deleted.
- **No permission (direct call).** "You don't have permission to perform this action."
- **Server failure.** An error toast appears and the switch flips back to its original state.

## Acceptance criteria

1. Given an active vehicle, when switched off and confirmed, then `IsActive = 0` in `VehicleMaster`, the row shows "Inactive", and past transfer records still reference it intact.
2. Given a vehicle is inactive, then it is hidden from vehicle selection dropdowns in Inbound Requisition and Stock Transfer.
3. Given an inactive vehicle, when switched on and confirmed, then `IsActive = 1` and toast "Vehicle activated" appears.
4. Given the user cancels the confirmation dialog, then no state changes and the switch stays in its original position.
5. Given a user without `edit_vehicle_master`, then the switch is hidden and a direct API call returns "You don't have permission to perform this action."
6. Given a vehicle ID belonging to CO-B called with a CO-A token, then "Vehicle not found" appears and nothing is modified.
7. Given any caller calling `POST /Setting/DeleteVehicle`, then "Vehicles cannot be deleted. Deactivate the vehicle instead." is returned and no row is deleted (D1).
8. Given server error during status toggle, then an error toast appears and the switch reverts.

## Platform notes

### RMS API

- `POST /Setting/ChangeVehicleStatus` `{ "vehicleId": "<guid>", "isActive": <bool> }` — `[CompanyAuthorize]`, `edit_vehicle_master` enforced, company from token.
- `POST /Setting/DeleteVehicle` — always refuses with standard business failure.

### Data — Tenant DB

- Updates `dbo.VehicleMaster` (`IsActive`, `LastUpdatedBy`, `LastUpdatedOn = GETUTCDATE()`).

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Confirmation modal with destructive styling on Deactivate (grey cancel, theme primary or error confirm button).
