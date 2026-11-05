# Stock Check — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `SCK-01-D01` | Location (S3) | default | `GetStockCheckSheet` L2; LB | Empty list (not the user's / not the company's) |
| `SCK-02-D01` | Not counted (S1) | default | `SaveStockCounts` with ONION only | Only ONION changes |
| `SCK-02-D02` | Company / user (S2) | default | save | `StockCheck.CompanyId` / `CreatedBy` = caller |
| `SCK-02-D03` | Cross-company (S3) | default | `SaveStockCounts` `warehouseId: LB`; old `AddEditStockCheck` with `stockId: SB` | "Select a location" / SB unchanged |
| `SCK-02-D04` | Conversion (S4) | default | ONION 500 "g"; ONION 1 "box" | 0.5 kg; "Onion can't be counted in box" |
| `SCK-02-D05` | Atomic (S5) | default | 3 lines, third is another company's item | Nothing saved; "One or more items aren't available at this location" |
| `SCK-02-D06` | Validation | default | qty −1; no lines; 1001 lines; note 600 chars; garbage ids | Messages; nothing saved |
| `SCK-02-D07` | Old bulk payload (D3) | default | `AddEditStockCheck` with every item, uncounted at 0 | Only rows with qty > 0 saved |
| `SCK-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `SCK-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `SCK-ALL-D03` | Double submit | default | same save twice quickly | Two count rows, stock = last count (idempotent value) |
