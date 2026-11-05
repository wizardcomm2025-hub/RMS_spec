# Transfer Report — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `TRP-01-D01` | People (T1, T2) | default | today | TRF-1 `sentBy` U1, `receivedBy` U2; TRF-2 `receivedBy` null |
| `TRP-01-D02` | In transit (T3, D4) | default | TRF-2 | `receivedQty`, `shortQty`, `shortValue` null; not in `totalShortValue` |
| `TRP-01-D03` | Date (T4) | default | range = today, a transfer `CreatedAt` today with `TransferDate` yesterday | Filtered on `TransferDate`: not returned |
| `TRP-01-D04` | Locations (T5, Q2) | default | `locationId` ST; `locationId` of CO-B | No rows; ST's own transfer to CK appears only without that filter |
| `TRP-01-D05` | Inactive (T6) | default | transfer with `IsActive = 0`; transfer with no active line; one inactive line | First two absent; inactive line absent |
| `TRP-01-D06` | Units (D6) | default | TRF-2; TRF-4 | ONION 500 g `sentValue` 20.00; SALT values null |
| `TRP-01-D07` | Over-receipt (D5) | default | TRF-4, `show` Short | `shortQty` -2; not returned under Short |
| `TRP-01-D08` | Totals | default | page 1 vs export | `totalSentValue` = Σ export `sentValue`; `totalShortValue` = Σ export `shortValue`; `totalCount` = distinct transfers |
| `TRP-01-D09` | Paging | default | `pageSize` 1 with TRF-1 | One transfer with **all** its lines; `pageSize` 0 / 1000 clamped to 1–100 |
| `TRP-01-D10` | Dates | default | `toDate` before `fromDate`; a 400-day range; garbage dates | "The end date can't be before the start date."; "Choose a period of up to one year."; 400 |
| `TRP-01-D11` | Over-posting | default | body with `companyId`, `userId` | Ignored; token's company and user used |
| `TRP-01-D12` | Garbage | default | `show` "X"; `search` with `'; DROP TABLE` | `show` treated as All; parameterised, no error |
| `TRP-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `TRP-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `TRP-ALL-D03` | Other company | TOKEN_U1 with `Origin: HOST-B` | each endpoint | Rejected by the tenant / company check |
