# 03 — Rename a menu section — tester test cases

- Use case: [`../03-rename-menu-section.md`](../03-rename-menu-section.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Inventory → Menu Section, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `MSE-03-T01` | AC 1 | R-SOUP in Starters | Edit Starters → "Starters & Soups"; **Save changes**. Open Inventory → Recipe → R-SOUP. | "Edit Menu Section — Starters"; toast "Menu section updated"; Recipe shows "Starters & Soups". |
| `MSE-03-T02` | AC 2 | — | Edit Beverages → " desserts ". | "Menu section name already exists"; nothing changes. |
| `MSE-03-T03` | AC 3 | — | Edit Old Specials → "Old Specials 2". | Opens with its own name; saved; still **Inactive**. |
| `MSE-03-T04` | AC 6 | — | Open Edit Beverages as A-ADMIN; A-ADMIN2 renames it; A-ADMIN saves. | "This menu section was changed by someone else. Reload and try again." |
| `MSE-03-T05` | AC 7 | — | Log in as A-VIEW. | No Edit icon. |
