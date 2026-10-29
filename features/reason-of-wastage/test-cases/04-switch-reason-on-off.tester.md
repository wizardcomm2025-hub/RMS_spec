# 04 — Switch a reason on or off — tester test cases

- Use case: [`../04-switch-reason-on-off.md`](../04-switch-reason-on-off.md) · Screen: [`../ui-ux.md`](../ui-ux.md#5-confirmations) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Setting → Reason of Wastage, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `RSN-04-T01` | AC 1 | W-1 uses Expired | Turn off Expired; read the dialog; **Deactivate**. Open the Wastage Report for W-1's date. | "Deactivate **Expired**? Staff won't be able to pick it in Wastage. Past wastage keeps this reason. You can activate it again at any time." Toast "Reason deactivated"; **Inactive**; W-1 still shows "Expired" in the report. |
| `RSN-04-T02` | AC 2, 3 | T01 done | Open (or reload) Stock → Wastage; add an item; open Reason. | Expired is not offered. |
| `RSN-04-T03` | AC 4 | — | Turn off Damaged; **Cancel**. Turn Old Reason on; **Activate**. | Cancel: nothing changes. Activate: "Activate **Old Reason**?" then "Reason activated". |
| `RSN-04-T04` | AC 5 | — | Log in as A-VIEW. As A-ADMIN look for any delete option. | No switch for A-VIEW; no delete anywhere. |
| `RSN-04-T05` | AC 8 | — | Go offline; turn off Damaged; confirm. | Error toast; switch returns; still Active after refresh. |
