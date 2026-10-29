# 02 — Add a vehicle

## Summary

A company user with `create_vehicle_master` registers a new vehicle by providing its Registration Number and Car Manufacturer Name. The system validates the input, checks registration uniqueness in real-time across the company (active and inactive vehicles), saves it as Active, and updates the list.

## Prerequisites

- **Backend.** `AddEditVechile` with server validation, exact messages, and `Status=false` on any refusal (see [`api-contract.md`](./api-contract.md#save-vehicle)); `ValidateRegNo` for live duplicate check.
- **Data.** `SP_AddEditVehicle` and `SP_ValidateRegNo` updated with tenant scoping and uniqueness constraints (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** [03](./03-edit-vehicle.md) shares the save contract.

## Actors & preconditions

- **Actor:** a company user with `create_vehicle_master`.
- **Trigger:** "＋ Add Vehicle" in the header or in the empty state.

## Main flow

1. The user clicks "＋ Add Vehicle"; the dialog "Add Vehicle" opens.
2. The user types the Registration Number; the field normalizes to uppercase as typed, and the system runs a debounced uniqueness check.
3. The user types the Car Manufacturer Name.
4. The user clicks "Add Vehicle"; the submit button shows a spinner and dialog closing is disabled while saving.
5. The system validates both fields, ensures registration uniqueness under a tenant lock, and inserts the vehicle for the caller's company: `IsActive = 1`, `CreatedBy` = caller, `CreatedOn` = UTC now.
6. The dialog closes, the toast "Vehicle created successfully" appears, and the list refreshes.

## Alternative flows

- **Empty / spaces only.**
  - Registration Number missing: "Registration number is required".
  - Manufacturer Name missing: "Car manufacturer name is required".
- **Over length cap.**
  - Registration Number > 20 characters: stopped at 20 in UI, refused by API with "Registration number must be 20 characters or less".
  - Manufacturer Name > 100 characters: stopped at 100 in UI, refused by API with "Car manufacturer name must be 100 characters or less".
- **Invalid characters.**
  - Registration Number containing spaces or special characters: "Registration number can only contain letters and numbers".
  - Manufacturer Name containing forbidden characters: "Car manufacturer name can only contain letters, numbers, spaces and & - ( ) ' / . ,".
- **Duplicate Registration Number.** The trimmed uppercase registration equals another vehicle of the company (active or inactive): "Registration number already exists" under the field and as a toast; nothing is saved.
- **Same registration in another company.** Allowed.
- **Over-posting.** Body containing `CompanyId`, `CreatedBy`, or `IsActive = false` is ignored; values derived from token and defaulted to Active.
- **No permission (direct call).** "You don't have permission to perform this action."
- **Double click.** Prevented; only one vehicle record is created.
- **Discard.** Closing a dirty form prompts "Discard changes?".
- **Server failure.** "Something went wrong. Please try again." (or "Failed to save vehicle"); the dialog stays open with user input preserved.

## Acceptance criteria

1. Given a valid registration "MH12AB1234" and manufacturer "Toyota", when saved, then "Vehicle created successfully" appears and the vehicle is listed as Active.
2. Given " mh12ab1234 " while "MH12AB1234" exists (active or inactive), then "Registration number already exists" and nothing is saved.
3. Given "MH12AB1234" exists only in CO-B, when CO-A adds "MH12AB1234", then it is saved successfully.
4. Given registration "MH-12-AB-1234" (with dashes), then "Registration number can only contain letters and numbers" appears and save is refused.
5. Given manufacturer "Tata & Sons (Commercial) - 2", then it is saved; given "Tata@Auto", then "Car manufacturer name can only contain letters, numbers, spaces and & - ( ) ' / . ," appears and save is refused.
6. Given a duplicate registration, then the duplicate message appears, never a success toast.
7. Given the body carries `CompanyId`, `CreatedBy`, or `IsActive = false`, then they are ignored: vehicle belongs to caller's company, created by caller, and set to Active.
8. Given a user without `create_vehicle_master`, then the Add Vehicle button is hidden and a direct API call returns "You don't have permission to perform this action."

## Platform notes

### RMS API

- `POST /Setting/AddEditVechile` with `VehicleId = "00000000-0000-0000-0000-000000000000"` — `[CompanyAuthorize]`, `create_vehicle_master` enforced on server.
- `POST /Users/ValidateRegNo` — `[CompanyAuthorize]`.

### Data — Tenant DB

- Inserts `dbo.VehicleMaster` under `sp_getapplock` per company; sets `CreatedOn = GETUTCDATE()`.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Uses `VehicleModal` styled like `LocationModal` with dark title bar, close icon, and cancel/submit buttons.
