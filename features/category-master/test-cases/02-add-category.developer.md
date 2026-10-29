# 02 — Add a category — developer test cases

- Use case: [`../02-add-category.md`](../02-add-category.md) · API: [`../api-contract.md`](../api-contract.md#save-category) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Save" = `POST /Setting/AddEditStockGroup`. **Valid body** =

```json
{ "StockGroupId": "00000000-0000-0000-0000-000000000000", "StockGroupName": "Dev Cat", "StockGroupAlias": "DEVCAT",
  "InventoryTypeId": <RM id of CO-A>, "ParentStockGroupId": "00000000-0000-0000-0000-000000000000" }
```

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `CAT-02-D01` | Authentication | no token · HOST-A | Save, valid body | 401; no row "Dev Cat" |
| `CAT-02-D02` | Authentication | `TOKEN_EXPIRED`, `TOKEN_TAMPERED` | Save | 401; nothing written |
| `CAT-02-D03` | Company isolation | `TOKEN_A_ADMIN` · **HOST-B** | Save | 401 "Token does not match this site's company."; nothing written |
| `CAT-02-D04` | Main flow | default | Save, valid body | `status: true`; one new row: CO-A, `ClassificationId = 1`, `IsActive = 1`, `IsDeleted = 0`, name "Dev Cat", code "DEVCAT", `CreatedBy` = A-ADMIN |
| `CAT-02-D05` | Over-posting | default | Valid body + `"CompanyId": <CO-B>`, `"CreatedBy": "<B-ADMIN id>"`, `"IsActive": false`, `"IsDeleted": true` | Row in CO-A, created by A-ADMIN, active, not deleted |
| `CAT-02-D06` | Over-posting | default | Valid body + `"ClasificationId": 2` | Row has `ClassificationId = 1` (appears in the list, not among asset categories) |
| `CAT-02-D07` | Company isolation | default | Valid body with `"InventoryTypeId": <CO-B's Packaging id>` | `status: false`, "Inventory type is required"; nothing written |
| `CAT-02-D08` | Validation | default | Name missing, null, "", "   " | `status: false`, "Category name is required" (or 400 without stack trace when missing); nothing written |
| `CAT-02-D09` | Validation | default | Name of 100 characters; 101; 100 + surrounding spaces | 100: saved. 101: "Category name must be 100 characters or less". 100 + spaces: saved trimmed |
| `CAT-02-D10` | Validation / injection | default | Name "Fruit@Home", "<script>x</script>", "x'); DROP TABLE StockGroup;--" | "Category name can only contain letters, numbers, spaces and & - ( ) ' / . ,"; nothing written; table intact |
| `CAT-02-D11` | Validation | default | Name " vegetables " | "Category name already exists"; nothing written |
| `CAT-02-D12` | Validation | default | Name "Old Stock" (inactive CAT-OLD) | "Category name already exists" |
| `CAT-02-D13` | Business rule | default | Name "Vegetables", code "VEG2", `InventoryTypeId` = FG | "Category name already exists" (CAT-FGVEG is FG). Then name "Spices", code "SPICE", RM → saved (CO-B's Spices doesn't count) |
| `CAT-02-D14` | Validation | default | Code missing, "", "  " | "Category code is required" |
| `CAT-02-D15` | Validation | default | Code "nut-1", "ABCDEFGHIJK" (11), "ÄBC" | "Category code must be 1–10 letters or digits"; code "nut1" → saved as "NUT1" |
| `CAT-02-D16` | Validation | default | Code " veg " with name "Nuts" (RM) | "Category code already exists" (case and spaces ignored); code "VEG" with name "Nuts" and type FG → "Category code already exists" (CAT-FGVEG) |
| `CAT-02-D17` | Validation | default | `InventoryTypeId` 0, -1, 999999, missing | "Inventory type is required" (or 400 without stack trace when missing) |
| `CAT-02-D18` | Rule bypass | default | `ParentStockGroupId` = CAT-LEAFY (a sub-category) | "Select a valid parent category"; nothing written |
| `CAT-02-D19` | Rule bypass | default | `ParentStockGroupId` = CAT-OLD (inactive) | "Select a valid parent category" |
| `CAT-02-D20` | Rule bypass | default | `ParentStockGroupId` = CAT-FGVEG (other type) | "Select a valid parent category" |
| `CAT-02-D21` | Company isolation | default | `ParentStockGroupId` = CAT-B-VEG; then ASSET-A; then a random GUID | "Select a valid parent category" each; nothing written |
| `CAT-02-D22` | Permission | `TOKEN_A_VIEW` · HOST-A | Save, valid body | "You don't have permission to perform this action."; nothing written |
| `CAT-02-D23` | Concurrency | default | Same valid body ("Race Cat", "RACE") twice in parallel | Exactly one row; the other "Category name already exists" |
| `CAT-02-D24` | Encoding | default | Name "Café Crème 2", code "CAFE2" | Saved; list returns the name unchanged |
| `CAT-02-D25` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Save; `ValidateStockGroupName`; `GetStockGroupByInventoryType` | 401 each; nothing written or returned |
| `CAT-02-D26` | Error hygiene | default | Save while `SP_AddEditStockGroup` fails (dev DB: rename temporarily) | "Something went wrong. Please try again."; no SQL or stack trace |
| `CAT-02-D27` | Authentication | no token | `POST /Users/ValidateStockGroupName` | 401 |
| `CAT-02-D28` | Company isolation | default | Validate "Spices" (CO-B only), RM | `rtrValue: false` |
| `CAT-02-D29` | Validation / injection | default | Validate " vegetables " RM → `true`; "Vegetables" FG with `inventoryTypeId` of FG → `true`; "' OR 1=1 --" → `false`, no error text | As stated |
| `CAT-02-D30` | Company isolation | default | `GetStockGroupByInventoryType` with CO-B's Packaging type id; then RM | Packaging: empty list. RM: Vegetables and Dairy only (active main) — no subs, no inactive, no CO-B rows |
