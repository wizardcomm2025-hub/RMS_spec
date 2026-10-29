# 02 — Add Driver

## User story

As an operations manager,
I want to register a new driver with their name, license number, and photo,
So that they can be assigned to dispatches and stock transfers.

---

## Acceptance criteria

1. **Modal Presentation**: Clicking "＋ Add Driver" opens a modal titled "Add Driver" with Cancel and "Add Driver" buttons.
2. **Form Fields**:
   - **Profile Picture**: Optional; compact row with a 64 px avatar, **Upload photo** (then **Change photo** / **Remove**), JPG or PNG up to **1 MB**. A bigger or other file is refused with "Profile picture must be a JPG or PNG image of 1 MB or less" and not attached.
   - **Driver Name**: Required, trimmed, max 100 characters with live character counter. Allowed characters: letters, numbers, spaces, and `& - ( ) ' / . ,`. Errors: "Driver name is required" · "Driver name can only contain letters, numbers, spaces and & - ( ) ' / . ,".
   - **License Number**: Required, trimmed, max 50 characters with live counter. Letters, numbers, spaces and hyphens; shown and saved uppercase. Errors: "License number is required" · "License number can only contain letters, numbers, hyphens and spaces".
3. **Live Duplicate License Check**:
   - As the user types the license number, debounced validation hits `POST /Users/ValidateLicNo`.
   - If the licence is used by another driver of the company — active **or inactive** — the error "License number already exists" displays and Submit is disabled. The check uses the caller's company from the token (never a company id in the body).
4. **Submission**:
   - Submitting sends `POST /Setting/AddEditDriver` with `DriverId: "00000000-0000-0000-0000-000000000000"`; the server saves it Active for the token's company (`isActive` / `companyId` in the body are ignored) and checks `create_driver_master`.
   - While submitting, the submit button displays a spinner and backdrop clicks are disabled.
   - On success: Modal closes, toast shows **"Driver created successfully"**, and the driver list refreshes immediately.
   - On any refusal the API returns `status: false` with the exact message ([`api-contract.md`](./api-contract.md#messages)); the toast shows it, a duplicate also shows under the licence field, and the modal stays open (D2).
5. **Discard Confirmation**:
   - If user typed data or uploaded an image and attempts to close the dialog (via Close icon, Cancel button, or Esc), a confirmation dialog appears: "Discard changes? You have unsaved changes. Discard them" with Cancel and Discard buttons.
