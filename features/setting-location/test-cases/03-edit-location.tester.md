# 03 — Edit a location — tester test cases

- Use case: [`../03-edit-location.md`](../03-edit-location.md)
- Screen design: [`../ui-ux.md`](../ui-ux.md)
- Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Location, unless stated.

## Test cases

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `LOC-03-T01` | AC 1 | — | Click Edit on Outlet One. Change City to "Nashik", untick/tick Can Take Requisition, add EMP-A2. Click **Save changes**. Reopen Edit. | Toast "Location updated"; the list shows the new values. Reopened form shows City "Nashik", the flag as set and the EMP-A2 chip. |
| `LOC-03-T02` | AC 2 | — | Edit Outlet One; change the name to "Kitchen". Wait, then click Save changes. | "Location name already exists" under Name while typing and again on save (inline and toast). Dialog stays open. After Cancel → Discard, the list still shows "Outlet One". |
| `LOC-03-T03` | Alt: same-name rename | — | Edit Kitchen; change the name to "KITCHEN " and save. | No duplicate error; "Location updated"; the row shows "KITCHEN". |
| `LOC-03-T04` | AC 10 | LOC-A3 is inactive | Edit Old Store. | The dialog opens with its data. **Default Location** is disabled with "Activate the location to make it default". Changing City and saving gives "Location updated". |
| `LOC-03-T05` | AC 9 | — | Log in as A-VIEW; open Location; check rows (desktop) and cards (375 px). | No Edit icon / Edit button on any row or card. |
| `LOC-03-T06` | Main flow 1 | Slow network (DevTools → Slow 3G) | Click Edit on Kitchen. | Title "Edit Location — Kitchen"; a loading overlay covers the form until the data arrives; then all fields are filled. |
| `LOC-03-T07` | Alt: discard | — | Edit Kitchen, change the City, click X, choose **Discard**. Reopen. | "Discard changes?" prompt; after Discard the old City is still there. |
| `LOC-03-T08` | Alt: no change | — | Edit Kitchen and click X without changing anything. | The dialog closes immediately, no prompt. |
| `LOC-03-T09` | Alt: server failure | — | Edit Kitchen, change City, go offline, click Save changes. | Error toast; dialog stays open with the change kept; after going online and refreshing, the old City is still stored. |
| `LOC-03-T10` | Alt: closing while saving | Slow 3G | Edit Kitchen, Save changes, then press Esc / click X immediately. | The dialog cannot be closed until the save finishes. |
| `LOC-03-T11` | AC 11 | Two browsers (or one normal + one private window), both logged in: A-ADMIN and A-ADMIN2 | Both open Edit on Outlet One. A-ADMIN2 changes City to "Two" and saves. Then A-ADMIN changes City to "One" and clicks Save changes. | A-ADMIN2: "Location updated". A-ADMIN: toast "This location was changed by someone else. Reload and try again."; the dialog stays open. After closing and refreshing, City is "Two". |
| `LOC-03-T12` | AC 11 | Two browsers as above | A-ADMIN opens Edit on Outlet One. In the other browser, A-ADMIN2 deactivates Outlet One. A-ADMIN changes the City and saves. | A-ADMIN: "This location was changed by someone else. Reload and try again."; Outlet One stays Inactive with its old City. |
