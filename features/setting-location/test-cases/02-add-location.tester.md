# 02 — Add a location — tester test cases

- Use case: [`../02-add-location.md`](../02-add-location.md)
- Screen design: [`../ui-ux.md`](../ui-ux.md)
- Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Location, unless stated.

## Test cases

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `LOC-02-T01` | AC 1 | — | Click **＋ Add Location**. Name "Riverside Cafe", Type Outlet, employees EMP-A1, Address Line 1 "12 River Rd", City "Pune", State "MH", Pincode "411001", tick Can Take Requisition. Click **Add Location**. | Toast "Location created". Dialog closes. "Riverside Cafe" is in the list: Type Outlet, Status **Active**, Requisition **Yes**, address "12 River Rd…". |
| `LOC-02-T02` | AC 8 | — | Open the Add dialog and look through every section. | Sections Basic, Staff, Address, Settings. Settings has **Default Location** and **Can Take Requisition** only — no POS Outlet field. |
| `LOC-02-T03` | AC 4 | LOC-A1 "Kitchen" exists | In Add, type "Kitchen" as the name and wait a moment. Then click Add Location with a type chosen. | Under Name: "Location name already exists" (while typing). On Add: the same message inline and as a toast; the dialog stays open; no new row in the list. |
| `LOC-02-T04` | AC 4 | — | Name " Kitchen " (spaces before and after). Add. | "Location name already exists"; nothing added. |
| `LOC-02-T05` | AC 4 | LOC-A3 "Old Store" is inactive | Name "Old Store". Add. | "Location name already exists"; nothing added. |
| `LOC-02-T06` | AC 4 | — | Name "kitchen" (lower case). Add. | "Location name already exists"; nothing added. |
| `LOC-02-T07` | AC 5 | — | Paste a 101-character name. Watch the counter. | The field stops at 100 characters; the counter shows 100/100. |
| `LOC-02-T08` | Alt: empty name | — | Leave Name empty, or type only spaces; pick a type; click Add Location. | "Location Name is required" under Name; dialog stays open. |
| `LOC-02-T09` | Alt: type missing | — | Name "Test Type", no Type. Add. | "Location Type is required"; dialog stays open. |
| `LOC-02-T10` | AC 6 | — | Try Pincode "12345", then "12a456", then empty, then "560001" (name "Pin Test", type Outlet). | "12345" and "12a456": "Pincode must be 6 digits" and nothing saved. Empty and "560001": saved ("Location created"). |
| `LOC-02-T11` | AC 10 | LOC-B2 "Central Store" exists in CO-B only | Add "Central Store", type Warehouse. | "Location created" — the name is free in CO-A. |
| `LOC-02-T12` | AC 9 | — | Log in as A-VIEW; open Location. Then, at 375 px, open the empty / list views. | No Add Location button anywhere (header or empty state). |
| `LOC-02-T13` | Alt: double click | — | Fill a valid new location "Double Tap"; double-click **Add Location** quickly. | The button shows a spinner and is disabled after the first click. Exactly one "Double Tap" row appears. |
| `LOC-02-T14` | Alt: discard | — | Open Add, type a name, click Cancel (or X). Choose **Discard**. Repeat and choose Cancel in the prompt. | Prompt "Discard changes?" — "You have unsaved changes. Discard them". Discard closes without saving; Cancel returns to the form with the text kept. |
| `LOC-02-T15` | Alt: closing while saving | Slow network (DevTools → Slow 3G) | Fill a valid location, click Add Location, then immediately press Esc, click X and click outside the dialog. | The dialog stays open until the save finishes. |
| `LOC-02-T16` | UI AC 1 | — | At 375 px, open Add. | The dialog is full screen; fields are stacked; only the content scrolls, the title and buttons stay fixed. |
| `LOC-02-T17` | AC 1 | T01 done | Open Edit on "Riverside Cafe". | Employee chip EMP-A1, the address, pincode and Can Take Requisition are as entered. |
| `LOC-02-T18` | Alt: server failure | — | Fill a valid location, switch the browser offline, click Add Location. | An error toast ("Failed to save location" or the server message); the dialog stays open with the input kept. Nothing added after going back online and refreshing. |
| `LOC-02-T19` | Injection (UI) | — | Add name `O'Brien's <b>Bar</b> & Grill`, type Outlet. | "Location created". The list shows the text exactly as typed (no bold, no broken page). |
