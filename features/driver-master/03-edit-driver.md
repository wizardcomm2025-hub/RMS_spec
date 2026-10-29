# 03 — Edit Driver

## User story

As an operations manager,
I want to update an existing driver's details (such as correcting their name or license, or uploading a new photo),
So that driver records remain accurate.

---

## Acceptance criteria

1. **Modal Presentation**:
   - Clicking the Edit icon shows a full-page loading indicator (`LoadingOverlay`) while `POST /Setting/GetDriverById` fetches fresh data.
   - If not found: Toast displays "Driver not found", list refreshes, and modal does not open.
   - If found: Opens modal titled "Edit Driver — <name>" with Cancel and "Save changes" buttons.
2. **Pre-population**:
   - Driver Name, License Number, and existing Profile Picture are pre-filled.
   - Character counters show current lengths.
3. **Validation**:
   - Name and License Number follow the same validation rules as Add Driver.
   - License Number duplicate validation excludes the current `DriverId`. Changing to another company driver's license triggers "License number already exists".
4. **Submission**:
   - Submitting sends `POST /Setting/AddEditDriver` with the existing `DriverId` and the `lastUpdatedOn` received from get-by-id; status is not changed by an edit. An unchanged photo is sent as `null` (keep).
   - The server only updates a driver of the caller's company (D3); another company's or an unknown id → "Driver not found"; a changed or missing `lastUpdatedOn` → "This driver was changed by someone else. Reload and try again."; needs `edit_driver_master`.
   - On success: Modal closes, toast shows **"Driver updated successfully"**, and the list refreshes.
   - On failure: Displays returned error message and modal stays open.
5. **Discard Confirmation**:
   - If the user modifies any field or photo and attempts to close, prompt "Discard changes?" appears.
