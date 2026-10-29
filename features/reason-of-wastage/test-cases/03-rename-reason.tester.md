# 03 — Rename a reason — tester test cases

- Use case: [`../03-rename-reason.md`](../03-rename-reason.md) · Screen: [`../ui-ux.md`](../ui-ux.md#3-dialog--form) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Setting → Reason of Wastage, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `RSN-03-T01` | AC 1 | W-1 uses Expired | Edit Expired → "Expired (past use-by)"; **Save changes**. Open Report → Wastage Report for W-1's date. | Dialog "Edit Reason — Expired"; toast "Reason updated"; the report shows "Expired (past use-by)". |
| `RSN-03-T02` | AC 2 | — | Edit Damaged → " spoiled "; save. Then Edit Damaged and save unchanged. | "Reason already exists"; nothing changes. Unchanged save: "Reason updated". |
| `RSN-03-T03` | AC 3 | — | Edit Old Reason → "Old Reason 2"; save. | "Reason updated"; still **Inactive**. |
| `RSN-03-T04` | AC 6 | — | Open Edit Spoiled as A-ADMIN. In another browser A-ADMIN2 renames Spoiled. A-ADMIN saves. | "This reason was changed by someone else. Reload and try again."; A-ADMIN2's text kept. |
| `RSN-03-T05` | AC 7 | — | Log in as A-VIEW. | No Edit icon on rows or cards. |
