# Raw Material — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_ADMIN`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `RAW-02-D01` | **Cross-company overwrite (D1)** | default | `AddEditStockItem` with `stockItemId: RM-B`, valid body | "Raw material not found"; RM-B unchanged, still CO-B's (today: taken over) |
| `RAW-02-D02` | Decimals (D3) | default | save `purchaseCost: 12.5`, `reorderLevel: 2.75` | Stored 12.50 and 2.7500 |
| `RAW-02-D03` | Vendor default (D4) | default | make V1 default on item A while V1 is non-default on item B | B unchanged |
| `RAW-02-D04` | Orphan stock (D5) | default | edit item A adding L2 | One `Stock` row for A at L2, `Qty 0`; no row with another id |
| `RAW-02-D05` | Company checks (D8) | default | category C-B / C-FG; brand of CO-B; `warehouseList: [L-B]`; vendor V-B | Category / brand / location / vendor messages; nothing saved |
| `RAW-02-D06` | Unit lock (Q2) | default | change `uom` on RM-USED | "The main unit can't change because this raw material is already in use." |
| `RAW-02-D07` | Validation | default | empty name; 101-char name; `conversion: 0`; malformed GUIDs | Field messages; 400 without stack trace |
| `RAW-02-D08` | Concurrency | default | stale / missing `updatedAt` on edit | "This raw material was changed by someone else. Reload and try again." |
| `RAW-02-D09` | Permission (D9) | `TOKEN_U_STAFF` | `AddEditStockItem`; `ChangeRawMaterialStatus`; `DelStockItem` | Permission message on each |
| `RAW-01-D01` | Cross-company read (D2) | default | `GetStockItemById` / `GetWarehouseItem` / `GetItemPriceHistory` with RM-B | Not found / empty |
| `RAW-04-D01` | Delete in use (Q1) | default | `DelStockItem` RM-USED; then RM-NEW; then RM-B | "This raw material is in use and can't be deleted. Deactivate it instead."; ok; not found |
| `RAW-03-D01` | Status | default | `ChangeRawMaterialStatus` RM-NEW off; RM-B off | ok; not found |
| `RAW-02-D10` | Over-posting | default | body `companyId`, `isActive: false`, `inventoryTypeId: 999`, `classificationId: 2` | Ignored: item stays Raw, CO-A, status unchanged |
| `RAW-02-D11` | Auth | no token; `TOKEN_SA` | every endpoint above | 401 |
