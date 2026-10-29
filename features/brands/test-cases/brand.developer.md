# Brand — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`; routes under `/Setting/`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `BRD-01-D01` | Authentication | no token; expired; tampered | `GetStockTypeWithPagination` | 401 |
| `BRD-01-D02` | Company isolation | A token · HOST-B | List | 401 "Token does not match this site's company." |
| `BRD-01-D03` | Main flow | default | List `{}` | All CO-A brands with `activeSubBrandCount`, `activeItemCount`, `updatedAt`; no Pepsi |
| `BRD-01-D04` | SuperAdmin | `TOKEN_SA` | List; save; status | 401 each |
| `BRD-02-D01` | Authentication (D1) | **no token** | `AddEditStockType`, `GetStockTypeById` (legacy) with BRD-B's id | 401 (today: anonymous read / write) |
| `BRD-02-D02` | Legacy refused (D1) | default | `AddEditStockType` `{ stockTypeId: BRD-COKE, stockTypeName: "X" }` | "This endpoint is no longer available."; unchanged |
| `BRD-02-D03` | Main flow | default | `AddEditStockDetailsType` empty id, "Nestle" | `status: true`, `data.stockTypeId`; row Active, CO-A, `CreatedAt` UTC |
| `BRD-02-D04` | Over-posting | default | Add with `isActive: false, isDeleted: true, isShortShelfLife: true, companyId: CO-B` | Saved Active in CO-A; `IsDeleted` 0; `IsShortShelfLife` default |
| `BRD-02-D05` | Validation / injection | default | name missing, "  ", "x'; DROP TABLE StockType;--", 101 chars | Exact messages; nothing saved |
| `BRD-02-D06` | Business rule | default | " AMUL ", "old brand" | "Brand name already exists", `status: false` |
| `BRD-02-D07` | Company isolation (D2) | default | Get / save BRD-B's id | "Brand not found"; Pepsi unchanged |
| `BRD-02-D08` | Concurrency | default | Stale `updatedAt`; missing `updatedAt`; 10 parallel adds of "Race" | Stale message ×2; one row, rest duplicate |
| `BRD-02-D09` | Rule bypass | default | Rename BRD-FREE with `isActive: false` | Renamed; stays Active |
| `BRD-02-D10` | Permission | `TOKEN_A_VIEW` | Add; rename | "You don't have permission to perform this action." |
| `BRD-03-D01` | Rule bypass (Q1) | default | `ChangeStockTypeStatus` off: BRD-COKE; BRD-AMUL | Sub-brands message; items message; unchanged |
| `BRD-03-D02` | Main flow | default | Off then on: BRD-FREE | `status: true` both |
| `BRD-03-D03` | Company isolation | default | Off: BRD-B | "Brand not found" |
| `BRD-03-D04` | Delete (D2) | default | `DeleteStockType` BRD-FREE; BRD-B | "Brands cannot be deleted. Deactivate the brand instead."; both unchanged |
| `BRD-03-D05` | Validation / permission / errors | default; A-VIEW | `isActive` "maybe"; A-VIEW off; procedure failing | 400 / `status: false`; permission message; generic error without stack trace |
