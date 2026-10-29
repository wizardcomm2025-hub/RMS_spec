# 04 — Switch a category on or off — tester test cases

- Use case: [`../04-switch-category-on-off.md`](../04-switch-category-on-off.md) · Screen: [`../ui-ux.md`](../ui-ux.md#5-confirmations) · Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Category Master, unless stated.

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `CAT-04-T01` | AC 1 | Expand Vegetables | Turn off Root Vegetables; read the dialog; click **Deactivate**. | Dialog "Deactivate **Root Vegetables**? It will be hidden from item, recipe and stock screens. You can activate it again at any time." with Cancel / Deactivate. Toast "Category deactivated"; row shows **Inactive**. |
| `CAT-04-T02` | AC 2 | T01 done | Open an item (Raw Material) and the Recipe dialog; open their category / sub-category dropdowns. | Root Vegetables is not offered. |
| `CAT-04-T03` | AC 3 | Leafy Greens active | Turn off Vegetables (RM); confirm. | Toast "Deactivate its sub-categories first."; Vegetables stays **Active**; switch back on. |
| `CAT-04-T04` | AC 4 | ITEM-SPINACH active in Leafy Greens | Turn off Leafy Greens; confirm. | "This category is used by active items. Move or deactivate them first."; stays Active. |
| `CAT-04-T05` | AC 5 | Old Stock and Old Sub inactive | Expand Old Stock; turn on Old Sub; confirm **Activate**. | Dialog "Activate **Old Sub**?"; toast "Activate the parent category first."; stays Inactive. Then activate Old Stock, then Old Sub → both "Category activated". |
| `CAT-04-T06` | AC 6 | — | Turn off Dairy; click **Cancel**. | Nothing changes; switch back on. |
| `CAT-04-T07` | AC 7 | — | Log in as A-VIEW; check rows and cards. As A-ADMIN, look for any delete option. | A-VIEW: no switch. Nobody: any delete action. |
| `CAT-04-T08` | Alt: failure | — | Go offline; turn off Dairy; confirm. | Error toast; switch returns to on; still Active after refreshing online. |
| `CAT-04-T09` | Filter | — | Deactivate Dairy; set Status = Inactive. | Dairy, Old Stock (and Root Vegetables if T01 ran) listed; reactivating Dairy removes it from that view. |
