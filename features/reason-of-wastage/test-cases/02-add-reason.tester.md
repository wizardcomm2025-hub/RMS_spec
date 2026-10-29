# 02 — Add a reason — tester test cases

- Use case: [`../02-add-reason.md`](../02-add-reason.md) · Screen: [`../ui-ux.md`](../ui-ux.md#3-dialog--form) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Setting → Reason of Wastage, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `RSN-02-T01` | AC 1 | — | **＋ Add Reason**; type "Dropped on floor"; **Add Reason**. | Dialog "Add Reason"; toast "Reason added"; listed as **Active**. |
| `RSN-02-T02` | AC 2 | — | Add " expired "; then add "old reason". | "Reason already exists" under the field as you type, and on save; nothing added. |
| `RSN-02-T03` | AC 5 | — | Add "Spill@Bar"; then "Damaged (transit) - box/crate, 2". | First: "Reason can only contain letters, numbers, spaces and & - ( ) ' / . ,". Second: "Reason added". |
| `RSN-02-T04` | AC 6 | — | Paste a 500-character valid reason; save. | No counter, no limit; "Reason added"; wraps in the list. |
| `RSN-02-T05` | AC 10, Q1 | Stock → Wastage open with two item rows | On row 2 click "＋"; type "Pest damage"; **Add Reason**. | Same dialog; toast "Reason added"; row 2 now shows "Pest damage"; row 1 unchanged; Setting page lists it as Active. |
| `RSN-02-T06` | AC 7 (D3) | Stock → Wastage open | Click "＋"; type "Spoiled"; **Add Reason**. | "Reason already exists"; dialog stays open; no "Reason added". |
| `RSN-02-T07` | AC 9 | — | Log in as A-VIEW; open this page and Stock → Wastage. | No **＋ Add Reason**; no "＋" next to the Reason dropdown. |
| `RSN-02-T08` | Alt: discard, failure | — | Type a reason; press Esc → **Discard**. Then go offline, add a reason. | "Discard changes?" then closes; offline: error toast, dialog stays open with the text. |
