# Wastage — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `WST-02-D01` | Stock row (W1) | default | old `AddEditWastage` with `stockId: SB` | SB unchanged; L1 stock used |
| `WST-02-D02` | Conversion (W2) | default | ONION 500 "g" | −0.5 kg; row 0.5 kg |
| `WST-02-D03` | Reason (W3) | default | reason 0; R-OFF; RB | "Select a reason for every line" |
| `WST-02-D04` | Over stock (W4) | default | ONION 11 kg; two parallel 6 kg | Refused / one saved |
| `WST-02-D05` | Barcode (W5) | default | BB; B1 with OIL; B1 5 kg | Refused each |
| `WST-02-D06` | Atomic (W6) | default | 3 lines, third over stock | Nothing saved |
| `WST-02-D07` | Location | default | L2; CO-B location | "Select a location" |
| `WST-03-D01` | Cancel | default | cancel twice; yesterday's entry; CO-B entry | ok then not found / TooLate / not found |
| `WST-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `WST-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `WST-ALL-D03` | Garbage | default | qty "abc", 501 lines, huge strings | 400 / validation |
