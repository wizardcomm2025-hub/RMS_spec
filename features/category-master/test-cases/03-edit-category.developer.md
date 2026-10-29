# 03 — Edit a category — developer test cases

- Use case: [`../03-edit-category.md`](../03-edit-category.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Get" = `POST /Setting/GetStockGroupById`. "Save" = `POST /Setting/AddEditStockGroup` with the category's current values **including `UpdatedAt`** from a fresh Get, plus the change.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `CAT-03-D01` | Authentication | no token | Get CAT-VEG | 401 |
| `CAT-03-D02` | Company isolation | default | Get CAT-B-VEG | `status: false`, "Category not found"; no CO-B data (D2 — today it returns the row) |
| `CAT-03-D03` | Company isolation | default | Get ASSET-A | "Category not found" |
| `CAT-03-D04` | Rule bypass | default | Get CAT-DEL | "Category not found" |
| `CAT-03-D05` | Over-posting / isolation | default | Get CAT-B-VEG with `"CompanyId": <CO-B>` in the body | "Category not found" |
| `CAT-03-D06` | Validation | default | Get with a random GUID; empty GUID; "abc"; no body | "Category not found", or 400 without stack trace for non-GUIDs |
| `CAT-03-D07` | Contract / error hygiene | default | Get CAT-OLD; then Get while `SP_GetStockGroupById` fails | `isActive: false`, `updatedAt` present (not null), `inventoryTypeName` "Raw Material". Failure: "Something went wrong. Please try again." |
| `CAT-03-D08` | Validation | default | Save CAT-DAIRY with name "Vegetables" | "Category name already exists"; row unchanged |
| `CAT-03-D09` | Company isolation | default | Save with `StockGroupId` = CAT-B-VEG (with its `UpdatedAt`) and name "Hijacked" | "Category not found"; CAT-B-VEG unchanged and still CO-B (D1 — today it is overwritten) |
| `CAT-03-D10` | Rule bypass | default | Save CAT-VEG with `ParentStockGroupId` = CAT-DAIRY | "A category with sub-categories can't be made a sub-category"; unchanged |
| `CAT-03-D11` | Rule bypass | default | Save CAT-DAIRY (used only by the **inactive** item ITEM-OLDMILK) with the FG type id; then save CAT-VEG (has sub-categories) with the FG type id | Both: "Inventory type can't be changed while the category has sub-categories or items" (items count whether active or not); both unchanged |
| `CAT-03-D12` | Rule bypass | default | Save CAT-ROOT with `ParentStockGroupId` = CAT-ROOT (itself) | "Select a valid parent category" |
| `CAT-03-D13` | Business rule | default | Save CAT-DAIRY with only the code changed, sending `"ImagePath": ""`, `"IsIntangible": false`, `"ClasificationId": 2` | `status: true`; `ImagePath` and `IsIntangible` unchanged; `ClassificationId` still 1 (D7) |
| `CAT-03-D14` | Concurrency | A-ADMIN, A-ADMIN2 | Both Get CAT-DAIRY; A-ADMIN saves; A-ADMIN2 saves with the old `UpdatedAt`. Repeat in parallel | Second: "This category was changed by someone else. Reload and try again."; first values remain. Parallel: exactly one succeeds |
| `CAT-03-D15` | Concurrency (bypass) | default | Save CAT-DAIRY without `UpdatedAt`; then with `null`; then after a status change | "This category was changed by someone else. Reload and try again." each; unchanged |
| `CAT-03-D16` | Permission | `TOKEN_A_VIEW` | Save CAT-DAIRY with a new code | "You don't have permission to perform this action."; unchanged |
| `CAT-03-D17` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Get CAT-VEG; Save CAT-VEG | 401 each |
