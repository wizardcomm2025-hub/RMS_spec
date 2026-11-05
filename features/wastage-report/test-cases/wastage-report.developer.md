# Wastage Report — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `WRP-01-D01` | Reason (W1) | default | Entries, today | `reason` set per row |
| `WRP-01-D02` | Rows (W4) | default | ONION, today | E1 and E2 with their own `qty`, `reason`, `recordedAt` |
| `WRP-01-D03` | Location (W3, W5) | default | `locationId` CK; O2; a CO-B location | CK rows, no error; no rows; no rows |
| `WRP-01-D04` | Cancelled (D7) | default | today | E5 absent, not in `totalValue` |
| `WRP-01-D05` | Units (D4) | default | E2; E6 | `qty` 0.5, `uom` kg, `value` 20.00; `uom` box, `value` null |
| `WRP-01-D06` | By item | default | `view` Item | ONION `qty` 3, `entryCount` 2; SALT `value` null, `entryCount` 1 |
| `WRP-01-D07` | By reason | default | `view` Reason | Spoiled `entryCount` 2, `itemCount` 2; a "No reason" row |
| `WRP-01-D08` | Totals | default | all views, page 1 vs export | `totalEntries`, `totalValue` the same in every view; `totalValue` = Σ export `value`; `totalCount` = rows in that view |
| `WRP-01-D09` | Date (W7) | default | entry at 00:00 on the day after `toDate` | Not returned |
| `WRP-01-D10` | Dates | default | `toDate` before `fromDate`; a 400-day range; garbage dates | "The end date can't be before the start date."; "Choose a period of up to one year."; 400 |
| `WRP-01-D11` | Paging | default | `pageSize` 0 / 1000; `pageNumber` -1 | Clamped to 1–100; page 1 |
| `WRP-01-D12` | Over-posting | default | body with `companyId`, `userId` | Ignored; token's company and user used |
| `WRP-01-D13` | Garbage | default | `view` "X"; `reasonId` of CO-B; `search` with `'; DROP TABLE` | `view` treated as Entries; no rows; parameterised, no error |
| `WRP-01-D14` | Export (W2) | default | `ExportWastageHistoryReport` | 200 with all rows of the view, ignoring paging |
| `WRP-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `WRP-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `WRP-ALL-D03` | Other company | TOKEN_U1 with `Origin: HOST-B` | each endpoint | Rejected by the tenant / company check |
