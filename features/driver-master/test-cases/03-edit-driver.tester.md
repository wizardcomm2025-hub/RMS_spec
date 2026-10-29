# Tester Test Cases — 03 Edit Driver

---

## Tests

| ID | AC | Persona | Action / Steps | Expected UI Result |
|---|---|---|---|---|
| `DRV-03-T01` | AC 1 | `A-FULL` | Click the Edit icon on driver `DRV_1` ("Rajesh Kumar"). | Full-page loader appears briefly, then modal opens titled "Edit Driver — Rajesh Kumar" with fields pre-filled. |
| `DRV-03-T02` | AC 2 | `A-FULL` | Inspect pre-filled fields. | Driver Name shows "Rajesh Kumar", License shows "DL-1420110012345", and Avatar preview renders existing photo. |
| `DRV-03-T03` | AC 3 | `A-FULL` | Change License Number to match `DRV_2` ("MH-0220190098765"). | Live validation displays: "License number already exists". The "Save changes" button is disabled. |
| `DRV-03-T04` | AC 3 | `A-FULL` | Change License Number back to its own original license ("DL-1420110012345"). | Duplicate error disappears. "Save changes" button is enabled. |
| `DRV-03-T05` | AC 4 | `A-FULL` | Update Name to "Rajesh Kumar Sharma" and click **Save changes**. | Submit button shows spinner; on completion, modal closes, toast displays **"Driver updated successfully"**, and the updated name shows in the table. |
| `DRV-03-T06` | AC 5 | `A-FULL` | Edit a field, then click the dialog Close button or Cancel. | Confirmation dialog appears: "Discard changes? You have unsaved changes. Discard them" with Cancel and Discard buttons. |
| `DRV-03-T07` | AC 1 | `A-FULL` | Click Edit on a driver that was deleted in the backend database. | Full-page loader closes, toast displays "Driver not found", list refreshes, and the modal does not open. |
