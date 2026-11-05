# Inbound Requisition — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `INB-02-D01` | Direction (I1) | default | dispatch A1 | `Transfer.FromWarehouseId` = CS, `ToWarehouseId` = K1; CS stock down |
| `INB-02-D02` | Race (I2) | default | two parallel dispatches of 4 kg each (stock 6) | One ok, one "Only … in stock"; stock never below 0 |
| `INB-02-D03` | Units (I2) | default | line in g: send 500 | Stock −0.5 kg; stock row unit stays kg |
| `INB-02-D04` | Checks (I3) | default | P1; RB; qty −1; qty 0 only; an item not on A1; more than remaining; more than stock | Messages; nothing saved |
| `INB-02-D05` | Numbers (I4) | default | 10 parallel dispatches on different requisitions | Distinct sequential numbers |
| `INB-02-D06` | Old endpoint (D5) | default | `SaveTransferDetailsV2` with From / To swapped | Direction taken from the requisition |
| `INB-02-D07` | Vehicle (Q3) | default | V-OFF; other company's driver | "Select a vehicle" / "Select a driver" |
| `INB-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `INB-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
