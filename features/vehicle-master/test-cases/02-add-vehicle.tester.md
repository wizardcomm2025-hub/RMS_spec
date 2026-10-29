# 02 — Add a vehicle — tester test cases

- Use case: [`../02-add-vehicle.md`](../02-add-vehicle.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Vehicle Master, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `VEH-02-T01` | AC 1 | — | Click **＋ Add Vehicle**. Type registration "DL02XY1234", manufacturer "Toyota Hilux". Click **Add Vehicle**. | Dialog closes; toast "Vehicle created successfully"; new row "DL02XY1234" listed with status **Active**. |
| `VEH-02-T02` | AC 2 | — | Click Add Vehicle. Type "mh12ab1234" (matches active vehicle `VEH-VAN-1`). Wait 300ms, then click Add Vehicle. | Under field: "Registration number already exists"; toast with duplicate message; dialog remains open; nothing saved. |
| `VEH-02-T03` | AC 2 | — | Type "mh14zz0001" (matches inactive vehicle `VEH-OLD`). | Under field: "Registration number already exists"; save is prevented. |
| `VEH-02-T04` | AC 3 | — | Type "KA01AB1111" (exists only in CO-B), manufacturer "Force". Click Add Vehicle. | "Vehicle created successfully"; vehicle is saved in CO-A. |
| `VEH-02-T05` | AC 4 | — | Type registration "MH-12-AB-1234" (with hyphens). | Field error: "Registration number can only contain letters and numbers"; Add button refused. |
| `VEH-02-T06` | AC 5 | — | Type registration "MH12KL5555", manufacturer "Tata & Sons (Commercial) - 2". Click Add Vehicle. Then try manufacturer "Tata@Motors". | First: "Vehicle created successfully". Second: "Car manufacturer name can only contain letters, numbers, spaces and & - ( ) ' / . ,"; nothing saved. |
| `VEH-02-T07` | AC 8 | — | Log in as A-VIEW. | Add Vehicle button is not visible on page. |
| `VEH-02-T08` | Alt: discard | — | Type registration "DL02ZZ9999", click Cancel, choose **Discard**. | Dialog closes immediately; nothing saved. |
| `VEH-02-T09` | Alt: double click | — | Fill valid registration and rapidly double-click Add Vehicle. | Button enters loading spinner on first click; exactly one vehicle is created. |
| `VEH-02-T10` | UI: uppercase | — | Type "mh12cd9999" in lowercase into Registration Number. | Field automatically displays "MH12CD9999" in uppercase as typed. |
