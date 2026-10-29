# 02 — Add a location

## Summary

A company user with `Location.Create` adds a new outlet, warehouse or kitchen. They give it a unique name and a type, and can assign employees, an address and the Default Location / Can Take Requisition flags. The system validates the input, saves the location as Active for the user's company, and shows it in the list. Every save marks the location as a POS outlet without affecting other locations.

## Prerequisites

- **Backend.** `SaveWareHouseDetails` with trim, length, pincode and duplicate validation, `Status=false` on failure, real POS handling and no bin changes (see [`api-contract.md`](./api-contract.md#save-location)). `ValidateLocName` for the live name check. `GetUserWarehouse` for the employee list.
- **Data.** `Sp_SaveWareHouseDetails` and `SP_ValidateLocName` changes (see [`data-model.md`](./data-model.md#stored-procedures)).
- **Depends on.** [01](./01-view-and-filter-locations.md) for the list it returns to. [05](./05-default-location.md) defines the Default flag; the two can be built in parallel against the same save contract.

## Actors & preconditions

- **Actor:** a company user with `Location.Create`.
- **Trigger:** "＋ Add Location" on the Location page.
- **Preconditions:** the company has at least one active location type.

## Main flow

1. The user clicks "＋ Add Location"; the dialog "Add Location" opens with empty fields.
2. The user enters Location Name; the system checks for duplicates as they type.
3. The user picks a Location Type, optionally employees, Address Line 1 / 2, City, State, Pincode, Default Location and Can Take Requisition.
4. The user clicks "Add Location"; the button shows a spinner and the dialog cannot be closed.
5. The system trims the name, validates every field and saves the location for the token's company with `IsActive = 1`, `IsDeleted = 0`, `IsPOSOutlet = 1`, `CreatedBy` and `UserResponsible` = the caller.
6. The system maps the chosen employees to the location.
7. The dialog closes, the toast "Location created" appears and the list refreshes with the new location.

## Alternative flows

- **Duplicate name.** The trimmed name equals (case-insensitive) the name of another non-deleted location of the company, active or inactive: inline error "Location name already exists" and toast; the dialog stays open; nothing is saved.
- **Name empty or spaces only.** "Location Name is required"; nothing saved.
- **Name over 100 characters.** The UI stops input at 100; the API rejects longer names.
- **Type missing.** "Location Type is required".
- **Bad pincode.** Not empty and not exactly 6 digits: "Pincode must be 6 digits".
- **Type not in the company's list.** "Location Type is required"; nothing saved (LOC-Q10).
- **Employee not valid.** Another company's, inactive or customer user, or an unknown id: "One or more employees are not valid."; nothing saved (LOC-Q6).
- **Double click.** The button is disabled while saving; only one location is created.
- **Discard.** Closing a changed form asks "Discard changes?"; Discard closes without saving.
- **Server failure.** Toast "Something went wrong. Please try again." (or "Failed to save location" when the server can't be reached); the dialog stays open with the input kept (LOC-Q2).
- **No permission.** A caller without `Location.Create` who calls the API directly: "You don't have permission to perform this action."; nothing saved (LOC-Q1).
- **Same name in another company.** Allowed — uniqueness is per company.

## Acceptance criteria

1. Given valid input, when the user adds a location, then "Location created" is shown and it appears in the list as Active with the chosen type, address, flags and employees.
2. Given any save, then the stored location has `IsPOSOutlet = 1`, and the POS flag of every other location is unchanged (old AC-1, D1).
3. Given a save, then no `BinWarehouse` row of any location changes (old AC-4, D4).
4. Given " Kitchen " while "Kitchen" exists in the company (active or inactive), then the save is rejected with "Location name already exists" (old AC-2, AC-7).
5. Given a name of 101 characters, then the UI does not accept it and the API rejects it with `Status=false` (old AC-7, D6).
6. Given pincode "12345" or "12a456", then the save is rejected; given "" or "560001", it is accepted (old AC-8).
7. Given the caller sends `CompanyId`, `UserId` or `CreatedBy` in the body, then they are ignored; the location belongs to the token's company and caller.
8. Given the Add form, then there is no POS Outlet field (old AC-1).
9. Given a user without `Location.Create`, then the Add button is hidden, and a direct API call returns "You don't have permission to perform this action." with nothing saved (LOC-Q1).
10. Given CO-B already has "Central Store", when A-ADMIN adds "Central Store" for CO-A, then it is saved.
11. Given an employee who is not active staff of the caller's company, or a type that is not one of the company's types, then the save is refused with "One or more employees are not valid." / "Location Type is required" and nothing is written (LOC-Q6, LOC-Q10).
12. Given a SuperAdmin token, then the save is refused with 401 and nothing is written (LOC-Q11).

## Platform notes

### RMS API

- `POST /Setting/SaveWareHouseDetails` with `WarehouseId = 0`, `[Authorize]`, server-side check of `Location.Create`. `CompanyId` and `UserId` from the token. Validation and messages: [`api-contract.md`](./api-contract.md#save-location).
- `POST /Users/ValidateLocName` `[Authorize]` for the live check (`locationId` null on add).
- `POST /Product/GetUserWarehouse` for the employee picker now requires `[Authorize]` (LOC-Q4).

### Data — Tenant DB

- Inserts `Warehouse`; inserts `UserWarehouseMapping` rows; never touches `BinWarehouse`; clears `IsDefault` on the company's other locations only when Default is set (see [05](./05-default-location.md)).

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Dialog layout, hints, counter and states: [`ui-ux.md`](./ui-ux.md#3-dialog--form).

## Configuration

- Location types per company. Name length (100) and pincode format are fixed rules (Q4, Q5).

## Out of scope

- Bins, contact numbers, third-party id — not on the form.

## Open questions

None open. LOC-Q1, Q2, Q3, Q4, Q6, Q10, Q11 resolved 2026-10-28 (see [README](./README.md#cross-cutting-open-questions)).
