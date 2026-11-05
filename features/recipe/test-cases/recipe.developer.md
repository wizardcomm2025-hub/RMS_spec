# Recipe — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_ADMIN`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `RCP-02-D01` | **Cross-company overwrite (D1)** | default | `AddEditRecipeItem` with `stockItemId: RB` | "Recipe not found"; RB unchanged |
| `RCP-02-D02` | Decimals (D2) | default | `salesPrice: 149.5`, `conversion: 0.5`, `yieldReport: 82.5` | Stored exactly |
| `RCP-02-D03` | Atomic lines (D3) | default | save that fails on the location check after valid lines | Old lines still there |
| `RCP-02-D04` | Stock rows (D4) | default | edit GRAVY removing L2 where its stock is 3 Kg | L2 `Stock` row unchanged (still active, 3 Kg) |
| `RCP-02-D05` | Loops (D5) | default | GRAVY with itself as ingredient; GRAVY with NEW-PASTE after NEW-PASTE uses GRAVY | Self / loop messages |
| `RCP-02-D06` | Company / type (D6) | default | ingredient from CO-B; PANEER-MASALA (finished) as ingredient; CO-B location / category | Messages; nothing saved |
| `RCP-02-D07` | Validation | default | no lines; empty name; `recipeQty: 0`; yield 120 | Field messages |
| `RCP-02-D08` | Concurrency | default | stale / missing `updatedAt` | Stale message |
| `RCP-02-D09` | Permission (D10) | `TOKEN_U_STAFF` | save; status; delete; `DelRecipe` | Permission message |
| `RCP-01-D01` | Cross-company read (D7) | default | `GetStockItemByIdForRecipe` RB; `DelRecipe` with a line of RB | Not found |
| `RCP-05-D01` | Delete in use (Q2, D8) | default | delete GRAVY; PANEER-MASALA; NEW-PASTE; RB | In use; in use; ok; not found |
| `RCP-03-D01` | Cost (Q1) | default | list after a new ONION receive at ₹50 | GRAVY and PANEER-MASALA costs rise; manual ones unchanged |
| `RCP-02-D10` | Over-posting | default | body `companyId`, `isActive: false`, `classificationId: 2` | Ignored |
| `RCP-02-D11` | Auth | no token; `TOKEN_SA` | every endpoint above | 401 |
| `RCP-02-D12` | Type lock (Q6) | default | `AddEditRecipeItem` edit of GRAVY with the Finished `inventoryTypeId` | "Recipe type can't be changed. Create a new recipe of the other type instead."; type unchanged |
