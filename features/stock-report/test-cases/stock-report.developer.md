# Stock Report — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `SRP-01-D01` | Mapping (S1) | default | location O1 | ONION 10 kg although not mapped |
| `SRP-01-D02` | Units (S2) | default | location CK | ONION 2.5 (2 kg + 500 g), unit kg |
| `SRP-01-D03` | Locations (S4, Q1) | default | `locationId` O2; CO-B location | No rows |
| `SRP-01-D04` | Totals | default | page 1 vs export | `totalValue` = Σ export values |
| `SRP-01-D05` | Paging | default | `pageSize` 0 / 1000 | Clamped to 1–100 |
| `SRP-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `SRP-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
