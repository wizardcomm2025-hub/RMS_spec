# 02 — Add a department — tester test cases

- Use case: [`../02-add-department.md`](../02-add-department.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Setting → Department, **＋ Add Department**, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `DEP-02-T01` | AC 1 | — | Name "Housekeeping"; **Add Department**. | Toast "Department created"; "Housekeeping" listed as **Active**. |
| `DEP-02-T02` | AC 2, AC 6 | — | Type "kitchen"; wait; click Add Department. | "Department name already exists" under the field while typing and on Add (toast); never "Department created"; nothing added (D2). |
| `DEP-02-T03` | AC 2 | Old Team is inactive | Name " Old Team ". Add. | "Department name already exists"; nothing added (D3). |
| `DEP-02-T04` | AC 3 | "Sales" exists only in CO-B | Name "Sales". Add. | "Department created". |
| `DEP-02-T05` | AC 4 | — | Paste 101 characters. | Field stops at 100; counter 100/100. |
| `DEP-02-T06` | AC 5 | — | Name "Front Office (Day) - 2" → Add. Then "Bar@Night". | First: created. Second: "Department name can only contain letters, numbers, spaces and & - ( ) ' / . ," and nothing added. |
| `DEP-02-T07` | AC 8 | — | Log in as A-VIEW. | No Add Department button (header or empty state). |
| `DEP-02-T08` | Alt: discard / closing | — | Type a name, Cancel → **Discard**. On Slow 3G: Add, then press Esc at once. | Discard closes without saving; the dialog can't be closed while saving. |
| `DEP-02-T09` | Alt: double click / failure | — | Valid name; double-click Add. Then offline. | One row only. Offline: error toast, dialog stays open with the text. |
