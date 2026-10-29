# 04 — Switch a menu section on or off — tester test cases

- Use case: [`../04-switch-menu-section-on-off.md`](../04-switch-menu-section-on-off.md) · Screen: [`../ui-ux.md`](../ui-ux.md) · Fixtures: [`README.md`](./README.md#test-data)

All cases: A-ADMIN on HOST-A, Inventory → Menu Section, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `MSE-04-T01` | AC 1 | — | Turn off Desserts; read; **Deactivate**. | "Deactivate **Desserts**? It can't be picked for recipes any more. You can activate it again at any time." → "Menu section deactivated"; **Inactive**. |
| `MSE-04-T02` | AC 2 | R-SOUP active in Starters | Turn off Starters; confirm. | "This menu section is used by active recipes. Move them to another section first."; stays Active. |
| `MSE-04-T03` | AC 3 | T01 done | Inventory → Recipe → Add; open Menu Section. | Desserts is not offered. |
| `MSE-04-T04` | AC 4 (D7) | R-OLDSPECIAL in Old Specials | Edit R-OLDSPECIAL; check Menu Section; save without changing it; reopen. | Shows "Old Specials (inactive)" selected; after saving it is still in Old Specials. |
| `MSE-04-T05` | Alt: cancel / activate | — | Turn off Beverages → **Cancel**; turn Old Specials on → **Activate**. | Cancel: nothing changes. "Activate **Old Specials**?" → "Menu section activated". |
| `MSE-04-T06` | AC 7 | — | As A-VIEW look at rows; as A-ADMIN look for delete. | No switch for A-VIEW; no delete anywhere. |
| `MSE-04-T07` | Alt: failure | — | Offline; turn off Beverages; confirm. | Error toast; switch returns. |
| `MSE-04-T08` | AC 3 | — | Open the POS (or Recipe dropdown) after T01. | Desserts isn't offered for new recipes; no active item disappeared (Q1). |
