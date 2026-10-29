# 03 — Edit a category — tester test cases

- Use case: [`../03-edit-category.md`](../03-edit-category.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Category Master, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `CAT-03-T01` | AC 1 | — | Edit Dairy; change code to "DRY"; **Save changes**. | Title "Edit Category — Dairy"; toast "Category updated"; code DRY in the list. |
| `CAT-03-T02` | AC 2 | — | Edit Dairy; change the name to "Vegetables"; wait; save. | "Category name already exists" live and on save; dialog stays open; after Discard the name is still Dairy. |
| `CAT-03-T03` | AC 3 | — | Edit Vegetables (RM). | Parent Category is disabled with "This category has sub-categories, so it stays a main category." |
| `CAT-03-T04` | AC 4 | — | Edit Vegetables (RM); change Category type to Finished Goods; save. | "Inventory type can't be changed while the category has sub-categories or items"; nothing changes. |
| `CAT-03-T05` | AC 7 | Two browsers: A-ADMIN and A-ADMIN2 | Both open Edit on Dairy. A-ADMIN2 changes the code to "D2" and saves. A-ADMIN changes the code to "D1" and saves. | A-ADMIN2: "Category updated". A-ADMIN: "This category was changed by someone else. Reload and try again."; the dialog stays open. After refresh the code is D2. |
| `CAT-03-T06` | AC 8 | — | Log in as A-VIEW. | No Edit icon on rows or cards. |
| `CAT-03-T07` | AC 9 | — | Edit Old Stock (inactive); change the code to "OLD1"; save. | "Category updated"; still Inactive. |
| `CAT-03-T08` | Alt: same-name rename | — | Edit Dairy; change the name to "DAIRY "; save. | No duplicate error; "Category updated"; shows "DAIRY". |
| `CAT-03-T09` | Alt: move sub | — | Edit Root Vegetables; set parent to Dairy; save. | "Category updated"; Root Vegetables now under Dairy ("1 sub"); Vegetables shows "1 sub". |
