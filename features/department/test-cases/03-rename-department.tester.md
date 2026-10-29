# 03 — Rename a department — tester test cases

- Use case: [`../03-rename-department.md`](../03-rename-department.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Setting → Department, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `DEP-03-T01` | AC 1 | — | Edit Store; rename to "Main Store"; **Save changes**. | Title "Edit Department — Store"; toast "Department updated"; "Main Store" listed. |
| `DEP-03-T02` | AC 2 | — | Edit Store; type "Kitchen"; save. | "Department name already exists" live and on save; dialog stays open; after Discard the name is unchanged. |
| `DEP-03-T03` | AC 3 | Old Team inactive | Edit Old Team; rename to "Old Team 2"; save. | "Department updated"; still **Inactive**. |
| `DEP-03-T04` | AC 6 | Two browsers: A-ADMIN, A-ADMIN2 | Both open Edit on Store. A-ADMIN2 renames to "Store B" and saves; A-ADMIN renames to "Store A" and saves. | A-ADMIN: "This department was changed by someone else. Reload and try again."; dialog stays open; after refresh "Store B". |
| `DEP-03-T05` | AC 7 | — | Log in as A-VIEW. | No Edit icon on rows or cards. |
