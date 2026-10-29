# 03 — Edit a vehicle — tester test cases

- Use case: [`../03-edit-vehicle.md`](../03-edit-vehicle.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Vehicle Master, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `VEH-03-T01` | AC 1 | — | Click Edit on `MH12AB1234`. Change manufacturer to "Toyota HiAce Premium" and click **Save changes**. | Modal title "Edit Vehicle — MH12AB1234"; toast "Vehicle updated successfully"; table displays new manufacturer name. |
| `VEH-03-T02` | AC 2 | — | Click Edit on `MH12AB1234`. Change registration to "MH12CD5678" (matches `VEH-TRUCK-1`). Wait 300ms, then click Save changes. | Under field: "Registration number already exists"; dialog stays open; nothing saved. |
| `VEH-03-T03` | AC 3 | `VEH-OLD` is inactive | Click Edit on `MH14ZZ0001` (Old Van). Change manufacturer to "Old Delivery Van". Save. | Toast "Vehicle updated successfully"; row retains **Inactive** status chip. |
| `VEH-03-T04` | AC 6 | Two browser sessions | Both A-ADMIN and A-ADMIN2 open Edit on `MH12AB1234`. A-ADMIN2 changes manufacturer to "Make 2" and saves. Then A-ADMIN changes to "Make 1" and saves. | A-ADMIN2: "Vehicle updated successfully". A-ADMIN: "This vehicle was changed by someone else. Reload and try again." Dialog remains open. |
| `VEH-03-T05` | AC 7 | — | Log in as A-VIEW. | Edit icon is hidden on all vehicle rows. |
| `VEH-03-T06` | AC 1 | — | Click Edit on `MH12AB1234`. Change registration to "mh12ab1234" (only case changed). Save. | Field upper-cases to "MH12AB1234"; no duplicate error against self; "Vehicle updated successfully". |
| `VEH-03-T07` | Alt: discard | — | Click Edit on `MH12AB1234`. Modify text, click Cancel, choose **Discard**. | Dialog closes without saving; original values remain. |
