# Stock Check Report — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `SCR-01-D01` | Date match (R1) | default | range = today | Rows saved with a time are returned |
| `SCR-01-D02` | Date match (R1) | default | range = 01/Nov | Midnight legacy row returned; `countedAt` time from `CreatedAt` |
| `SCR-01-D03` | Every count (R4) | default | ONION, today | Two rows |
| `SCR-01-D04` | Source (R3) | default | location O1 | BEANS present; items never counted absent |
| `SCR-01-D05` | Units (D4) | default | ONION 01/Nov; SALT | `qty` 0.5, `uom` kg, `value` 20.00; SALT `value` null, `uom` box |
| `SCR-01-D06` | Creation rows (D6) | default | today | GARLIC absent, TOMATO (real 0) present |
| `SCR-01-D07` | Locations (R5, D1) | default | `locationId` O2; CO-B location | No rows |
| `SCR-01-D08` | Totals | default | page 1 vs export | `totalValue` = Σ export `value`; `totalCount` = export rows |
| `SCR-01-D09` | Paging | default | `pageSize` 0 / 1000; `pageNumber` -1 | Clamped to 1–100; page 1 |
| `SCR-01-D10` | Dates | default | `toDate` before `fromDate`; a 400-day range; garbage dates | "The end date can't be before the start date."; "Choose a period of up to one year."; 400 |
| `SCR-01-D11` | Over-posting | default | body with `companyId`, `userId` | Ignored; token's company and user used |
| `SCR-01-D12` | Garbage | default | `search` with `'; DROP TABLE` | Parameterised; no match, no error |
| `SCR-01-D13` | Export (R2) | default | `ExportStockCheckReport` | 200 with all rows, ignoring paging |
| `SCR-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `SCR-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `SCR-ALL-D03` | Other company | TOKEN_U1 with `Origin: HOST-B` | each endpoint | Rejected by the tenant / company check |
