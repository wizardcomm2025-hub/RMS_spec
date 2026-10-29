# 04 — Switch a location on or off — tester test cases

- Use case: [`../04-switch-location-on-off.md`](../04-switch-location-on-off.md)
- Screen design: [`../ui-ux.md`](../ui-ux.md#5-confirmations)
- Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Location, unless stated.

## Test cases

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `LOC-04-T01` | AC 1 | LOC-A1 Kitchen is active and Default | Turn off Kitchen's switch. Read the dialog. Click **Deactivate**. | Dialog: "Deactivate **Kitchen**? It will be hidden from stock, requisition, POS and user screens. You can activate it again at any time." with Cancel and Deactivate. After Deactivate: toast "Location deactivated"; Kitchen shows **Inactive**; its **Default** badge is gone; it stays in this list. |
| `LOC-04-T02` | AC 2 | T01 done | Open Item Requisition (and Stock Check) and open their location dropdowns. | Kitchen is not offered. Outlet One is. |
| `LOC-04-T03` | AC 3 | T01 done | Turn Kitchen's switch on; confirm **Activate**. Open Edit. | Dialog "Activate **Kitchen**?" with Cancel and Activate. Toast "Location activated"; status **Active**; no Default badge. Edit shows employees EMP-A1 and EMP-A2 as before; Default unticked. |
| `LOC-04-T04` | AC 4 | — | Turn off Outlet One's switch; click **Cancel**. | No toast; Outlet One stays **Active**; the switch is back on. |
| `LOC-04-T05` | AC 5 | — | Log in as A-VIEW; look at every row and card. Also as A-ADMIN, look for any delete option (row, card, dialog). | A-VIEW: no switch. A-ADMIN: no delete action anywhere. |
| `LOC-04-T06` | Alt: server failure | — | Go offline; turn off Outlet One; confirm Deactivate. | Error toast; the switch returns to on; status stays **Active** after refreshing online. |
| `LOC-04-T07` | UI AC 4 | — | With the keyboard only: Tab to Outlet One's switch, press Space, then Tab to Cancel and press Enter. | The confirmation opens and closes with the keyboard; focus returns to the switch. |
| `LOC-04-T08` | AC 1 | — | Filter Status = Inactive after deactivating Outlet One; then reactivate it. | Outlet One appears under Inactive; after reactivation it disappears from the Inactive filter. |
