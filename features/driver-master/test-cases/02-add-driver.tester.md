# Tester Test Cases — 02 Add Driver

---

## Tests

| ID | AC | Persona | Action / Steps | Expected UI Result |
|---|---|---|---|---|
| `DRV-02-T01` | AC 1, 2 | `A-FULL` | Click **＋ Add Driver**. | Dialog opens titled "Add Driver". Contains the profile picture row (avatar, "JPG or PNG, up to 1 MB", **Upload photo**), Driver Name field, License Number field, and buttons Cancel and "Add Driver". |
| `DRV-02-T02` | AC 2 | `A-FULL` | Click "Add Driver" with empty form. | Validation messages appear: "Driver name is required" and "License number is required". |
| `DRV-02-T03` | AC 2 | `A-FULL` | Click **Upload photo** and pick a JPG under 1 MB. | The avatar shows the photo; the buttons become **Change photo** and **Remove**. The whole dialog fits without scrolling at 1366×768 and the Cancel / Add Driver buttons stay visible. |
| `DRV-02-T04` | AC 2 | `A-FULL` | Click **Remove**. | The avatar shows the person icon again and the button reads **Upload photo**. |
| `DRV-02-T05` | AC 3 | `A-FULL` | Enter an existing driver's license number (e.g. `DL-1420110012345`). | Live error displays under License Number: "License number already exists". The "Add Driver" button is disabled. |
| `DRV-02-T06` | AC 4 | `A-FULL` | Enter valid Name "Sunil Verma" and unused License "UP-1620230055441", then click **Add Driver**. | Submit button shows spinner; on completion, modal closes, toast displays **"Driver created successfully"**, and the new driver appears in the list. |
| `DRV-02-T07` | AC 5 | `A-FULL` | Click **＋ Add Driver**, type "Some text" in Driver Name, and click the dialog Close (X) icon or Cancel. | Confirmation dialog appears: "Discard changes? You have unsaved changes. Discard them" with Cancel and Discard buttons. |
| `DRV-02-T08` | AC 5 | `A-FULL` | In the discard dialog, click "Discard". | Modal closes and entered data is discarded. |
| `DRV-02-T09` | AC 5 | `A-FULL` | In the discard dialog, click "Cancel". | Discard dialog closes and entered data remains intact in the form. |
| `DRV-02-T10` | Q4 | `A-FULL` | In Add Driver, drop a 2 MB JPG, then a GIF, then a 500 KB PNG. | First two: "Profile picture must be a JPG or PNG image of 1 MB or less" and no preview; the PNG shows as the preview. |
