# 04 — Switch a vehicle on or off — tester test cases

- Use case: [`../04-switch-vehicle-on-off.md`](../04-switch-vehicle-on-off.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Vehicle Master, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `VEH-04-T01` | AC 1 | `VEH-VAN-1` is active | Turn off the switch for `MH12AB1234`. Click **Deactivate** in the confirmation modal. | Modal displays: "Deactivate **MH12AB1234**? Staff won't be able to assign it to delivery or stock transfers..."; on confirm, toast "Vehicle deactivated" appears and status chip flips to **Inactive**. |
| `VEH-04-T02` | AC 2 | `VEH-VAN-1` is inactive | Open Stock → Inbound Requisition (or Transfer Dispatch) and open the Vehicle dropdown. | `MH12AB1234` is not offered in the dropdown list. |
| `VEH-04-T03` | AC 3 | `VEH-OLD` is inactive | Turn on the switch for `MH14ZZ0001` (Old Van). Confirm in modal with **Activate**. | Toast "Vehicle activated" appears; status chip updates to **Active**; vehicle is now offered in transfer dropdowns. |
| `VEH-04-T04` | AC 4 | `VEH-VAN-1` is active | Turn off the switch on `MH12AB1234`. In the confirmation modal, click **Cancel**. | Modal closes; switch remains ON (Active); no toast, no changes saved. |
| `VEH-04-T05` | AC 5 | — | Log in as A-VIEW. | Active switch is completely hidden on every row; user cannot toggle status. |
| `VEH-04-T06` | Alt: server error | — | Turn network Offline in browser DevTools. Click switch on `MH12AB1234` and confirm. | Error toast appears; switch automatically reverts back to its previous position. |
