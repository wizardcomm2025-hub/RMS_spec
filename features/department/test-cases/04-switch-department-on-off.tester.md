# 04 — Switch a department on or off — tester test cases

- Use case: [`../04-switch-department-on-off.md`](../04-switch-department-on-off.md) · Screen: [`../ui-ux.md`](../ui-ux.md#5-confirmations) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Setting → Department, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `DEP-04-T01` | AC 1 | — | Turn off Store; read the dialog; **Deactivate**. | "Deactivate **Store**? It will be hidden from user, roster and other department lists. You can activate it again at any time." Toast "Department deactivated"; **Inactive**. |
| `DEP-04-T02` | AC 2 | T01 done | Open User Management → User List → Add User; open Department. | Store is not offered. |
| `DEP-04-T03` | AC 3 | U-COOK active in Kitchen | Turn off Kitchen; confirm. | "This department has active users. Move or deactivate them first."; stays Active; switch back on. |
| `DEP-04-T04` | AC 4 | RP-LUNCH active in Service | Turn off Service; confirm. | "This department is used by active roster patterns. Deactivate them first."; stays Active. |
| `DEP-04-T05` | AC 6 | — | Turn off Store; **Cancel**. Then turn Old Team on and confirm **Activate**. | Cancel: nothing changes. Activate: "Activate **Old Team**?" then "Department activated". |
| `DEP-04-T06` | AC 7 | — | Log in as A-VIEW; check rows and cards. As A-ADMIN, look for any delete option. | No switch for A-VIEW; no delete anywhere. |
| `DEP-04-T07` | Alt: failure | — | Offline; turn off Store; confirm. | Error toast; switch returns; still Active after refresh. |
