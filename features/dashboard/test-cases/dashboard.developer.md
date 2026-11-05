# Dashboard — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `DSH-ALL-D01` | Auth | none / expired / tampered | any endpoint | 401 |
| `DSH-ALL-D02` | Auth | super-admin token | any endpoint | 401 (company users only) |
| `DSH-ALL-D03` | Tenant | TOKEN_U1, `Origin: HOST-B` | any endpoint | Refused by tenant check |
| `DSH-ALL-D04` | Permission | U-STAFF | `GetDashboardStock`, `GetDashboardSales`, `GetDashboardPurchases` | `Status false`, "You don't have permission to perform this action." |
| `DSH-ALL-D05` | Permission | U-STAFF | `GetDashboardSections` | All false |
| `DSH-ALL-D06` | Over-posting | default | body with `companyId`, `userId` of CO-B | Ignored; own data |
| `DSH-01-D01` | Cap (H2) | default | `GetDashboardStock` | `itemCount` = all items, not 10; `items` at most 8 |
| `DSH-01-D02` | Units (H8) | default | `GetDashboardStock`, CK | ONION quantity 2.5, unit kg, status Low |
| `DSH-01-D03` | Location (H3) | default | `GetDashboardSales`, `locationId` = O2 | Zeros (not my location) |
| `DSH-01-D04` | Other company | default | `locationId` of CO-B | Zeros |
| `DSH-01-D05` | Dates | default | to before from; 367 days; missing date; `"abc"` | The three messages / 400 for bad JSON type; nothing returned |
| `DSH-01-D06` | Agreement (D1) | default | same filters: `GetDashboardPeriod` vs Wastage Report | Same value |
| `DSH-01-D07` | Partial rights | wastage only | `GetDashboardPeriod` | Production parts `null` |
| `DSH-01-D08` | Sales | default | `GetDashboardSales` today, CK | 600, 3 bills, unpaid 300 |
| `DSH-02-D01` | Cap (H1) | default | `GetDashboardAttention` | `toApprove` 7, `toReceive` 6 |
| `DSH-02-D02` | Receive (H4) | default | transfer created by U1 going to O2 | Not counted |
| `DSH-02-D03` | Today (H5) | default | count at 15:30 today at CK | CK not in `locationsNotCounted` |
| `DSH-02-D04` | Row permission | no approve right | `GetDashboardAttention` | `toApprove` null |
| `DSH-OLD-D01` | Old endpoints (D7) | none / U-no-dashboard | `GetTransferWidget` | 401 / no permission |
