# Recipe — tester test cases

| ID | Covers | Steps | Expected result |
|---|---|---|---|
| `RCP-01-T01` | 01 | Open Recipe; switch tabs; filter Category, Menu Section, Status Inactive; page 2. | Exact counts and footer; server results; Clear filters resets. |
| `RCP-03-T01` | 03 | Open GRAVY. | Lines: onion ₹40 / Kg → ₹80; oil ₹0.15 / ml → ₹75; batch ₹155; cost ₹48.44 / Kg "Calculated". |
| `RCP-03-T02` | 03 | Open PANEER-MASALA. | GRAVY line 250 g → ₹12.11; food cost % uses the calculated cost. |
| `RCP-03-T03` | 03 | Turn on Manual cost, type 60, save; reopen; turn it off, save. | List 60.00 with "Manual"; then back to the calculated value. |
| `RCP-02-T01` | 02 | Add a semi-finished recipe with two ingredients; price 149.50, conversion 0.5. | "Recipe created"; values kept exactly. |
| `RCP-02-T02` | 02 | Edit GRAVY; search "Gravy" in ingredients. | GRAVY itself not offered; no duplicates. |
| `RCP-02-T03` | 02 | Edit NEW-PASTE: add GRAVY; then edit GRAVY: add NEW-PASTE. | Second save: "This would make a loop: NEW-PASTE already uses this recipe". |
| `RCP-02-T04` | 02 | Edit PANEER-MASALA. | Unit read-only with "Can't change: already in use". |
| `RCP-04-T01` | 04 | Turn off NEW-PASTE; search it as an ingredient elsewhere. | "Recipe deactivated"; not offered. |
| `RCP-05-T01` | 05 | Hover Delete on GRAVY; delete NEW-PASTE. | GRAVY: disabled with the reason; NEW-PASTE: "Recipe deleted". |
| `RCP-UI-T01` | UI | As U-STAFF; at 375 px. | No Add / Edit / Delete / switch; phone cards. |
| `RCP-02-T05` | 02, Q6 | Edit GRAVY; look at Recipe type. | Type cards read-only, the other type greyed out; note "Type can't change after the recipe is created…". |
