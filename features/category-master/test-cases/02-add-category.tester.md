# 02 — Add a category — tester test cases

- Use case: [`../02-add-category.md`](../02-add-category.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A, open Setting → Category Master, click **＋ Add Category**, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `CAT-02-T01` | AC 1 | — | Name "Bakery", code "BAK", type Raw Material, parent None. Click **Add Category**. | Toast "Category created"; "Bakery" is a main row, code BAK, type Raw Material, **Active**. |
| `CAT-02-T02` | AC 2 | — | Name "Herbs", code "HERB", type Raw Material, parent Vegetables. Add. | "Category created"; Vegetables shows "3 sub" and is expanded with Herbs under it. |
| `CAT-02-T03` | AC 3 | — | Type "vegetables" as the name (type Raw Material); wait; then Add. | Under Name: "Category name already exists" while typing and on Add (plus toast); dialog stays open; nothing added. |
| `CAT-02-T04` | AC 3 | CAT-OLD is inactive | Name " Old Stock " (spaces), code "OS2", Raw Material. Add. | "Category name already exists"; nothing added. |
| `CAT-02-T05` | AC 4 | — | Name "Dairy", code "DAIRY", type **Finished Goods**. Add. | "Category created" — same name and code are allowed in another type. |
| `CAT-02-T06` | AC 5 | — | Name "Nuts", type Raw Material, code "VEG". Add. | "Category code already exists"; nothing added. |
| `CAT-02-T07` | AC 5 | — | Name "Nuts", Raw Material; type "nut-1" into Category Code. Add. | The field shows "NUT1" as you type (the dash is dropped, letters upper-cased); "Category created" with code NUT1. |
| `CAT-02-T08` | AC 6 | — | Paste a 101-character name. | The field stops at 100; counter 100/100. |
| `CAT-02-T09` | AC 10 | — | Type the name "Fresh Vegetables" without touching the code; then type your own code; then change the name. | Code shows "FREVEG"; after you type your own code, changing the name no longer changes it. |
| `CAT-02-T10` | AC 9 | — | Log in as A-VIEW. | No Add Category button (header or empty state). |
| `CAT-02-T11` | Alt: characters | — | Name "Fruit & Veg (Imported) 2", code "FV2". Add. Then try name "Fruit@Home". | First: "Category created". Second: "Category name can only contain letters, numbers, spaces and & - ( ) ' / . ," and nothing added. |
| `CAT-02-T12` | Alt: type change | — | Pick parent Vegetables, then change Category type to Finished Goods. | The parent resets to None; the parent list shows only Finished Goods main categories. |
| `CAT-02-T13` | Alt: discard / closing | — | Type a name, click Cancel → choose **Discard**. Repeat on Slow 3G: click Add and press Esc at once. | Discard closes without saving. While saving, the dialog can't be closed. |
| `CAT-02-T14` | Alt: double click / failure | — | Fill a valid category and double-click Add. Then repeat with the browser offline. | One row only. Offline: error toast, dialog stays open with the input kept. |
| `CAT-02-T15` | UI (searchable parent) | Raw Material has several main categories | Open Add; click Parent Category; type "dai"; pick Dairy; then click × in the field. | Typing filters the list to Dairy; after picking, the field shows Dairy; × clears it back to the placeholder "None (main category)". Typing "zzz" shows "No matches". Sub-categories and Finished Goods categories are never offered. |
