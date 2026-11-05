# Barcode Report — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `BRP-01-D01` | Dates (B1) | default | B3 | `mfgDate`, `expDate` are ISO dates; `mfgDate` is 2 January |
| `BRP-01-D02` | Agreement (B2) | default | each `show` | `itemQty`, `itemValue`, `barcodeCount` equal the sum of the returned lines; no item without lines |
| `BRP-01-D03` | Units (B3, D4) | default | ONION | One item, `mainUom` kg, `itemQty` 8.5; B2 `remainingQty` 500, `uom` g, `value` 20.00 |
| `BRP-01-D04` | Unknown unit (D4) | default | SALT | `itemQty` and `value` null, not in `totalValue` |
| `BRP-01-D05` | Status (D1) | default | expiry today; expiry T+30; T+31; T−1; no expiry; remaining 0 with T+5 | `Expiring`; `Expiring`; `""`; `Expired`; `""`; `UsedUp` |
| `BRP-01-D06` | Locations (B5) | default | `locationId` O2; a CO-B location | No rows |
| `BRP-01-D07` | No location (D2) | default | add a barcode with `LocationId` NULL; request without, then with `locationId` | Listed with empty `warehouseName`; left out |
| `BRP-01-D08` | Search (D3) | default | item alias; barcode number; both no match | All its barcodes; that barcode only; no rows |
| `BRP-01-D09` | Totals | default | page 1 vs export | `totalValue` = Σ export `value`; `totalBarcodes` = export rows; `totalCount` = distinct items |
| `BRP-01-D10` | Paging | default | `pageSize` 1 with ONION | One item with all its lines; `pageSize` 0 / 1000 clamped to 1–100; `pageNumber` -1 = page 1 |
| `BRP-01-D11` | Order (D6) | default | `show` Expired, several items | Nearest expiry first |
| `BRP-01-D12` | Over-posting | default | body with `companyId`, `userId` | Ignored; token's company and user used |
| `BRP-01-D13` | Garbage | default | `show` "X"; `search` with `'; DROP TABLE` | `show` treated as InStock; parameterised, no error |
| `BRP-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `BRP-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `BRP-ALL-D03` | Other company | TOKEN_U1 with `Origin: HOST-B` | each endpoint | Rejected by the tenant / company check |
