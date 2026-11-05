# Opening Stock Report — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request / action | Expected result |
|---|---|---|---|---|
| `OSR-03-D01` | Ledger health | SSMS | `health_stock_ledger.sql` after the fixtures | 0 item + location pairs where Σ ledger ≠ current stock |
| `OSR-03-D02` | Units | SSMS | update a stock row kept in g by +500 | Ledger +0.5 (kg) |
| `OSR-03-D03` | Untagged | SSMS | `UPDATE Stock SET Qty = Qty + 1` on one row | One ledger row, type Other, dated today |
| `OSR-03-D04` | Rollback | SSMS | failing Dispatch (over stock) | No ledger rows |
| `OSR-03-D05` | Multi-row | SSMS | update 3 rows in one statement | 3 ledger rows |
| `OSR-03-D06` | Business date | default | production dated yesterday | Ledger `MoveDate` = yesterday |
| `OSR-01-D01` | Identity | default | any range | opening + in − out + count = closing per line |
| `OSR-01-D02` | Locations | default | `locationId` O2 / CO-B location | No rows |
| `OSR-01-D03` | Dates | default | to = tomorrow; from > to | "The dates can't be in the future" / "From date must be on or before To date" |
| `OSR-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `OSR-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
