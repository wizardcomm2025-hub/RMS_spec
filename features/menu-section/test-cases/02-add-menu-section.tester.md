# 02 — Add a menu section — tester test cases

- Use case: [`../02-add-menu-section.md`](../02-add-menu-section.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Inventory → Menu Section, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `MSE-02-T01` | AC 1 | — | **＋ Add Menu Section**; type "Combos"; **Add Menu Section**. | Dialog "Add Menu Section"; toast "Menu section created"; listed Active. |
| `MSE-02-T02` | AC 2 | — | Add " starters "; then "old specials". | "Menu section name already exists" as you type and on save; no success toast; nothing added. |
| `MSE-02-T03` | AC 4 | — | Add "Soup@Night"; then "Chef's Special (Veg) - 2". | First: "Menu section name can only contain letters, numbers, spaces and & - ( ) ' / . ,". Second: created. |
| `MSE-02-T04` | AC 4 | — | Type 105 characters. | The field stops at 100; counter "100/100". |
| `MSE-02-T05` | AC 6 | — | Log in as A-VIEW. | No **＋ Add Menu Section** in the header or the empty state. |
| `MSE-02-T06` | Alt: discard | — | Type a name; Esc → **Discard**. | "Discard changes?" then the dialog closes. |
| `MSE-02-T07` | Alt: failure | — | Go offline; add a section. | Error toast; dialog stays open with the name. |
