# Item Receive — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `IRC-02-D01` | Double receive (R1) | default | `ReceiveTransfer` T1 twice; then two parallel calls on a fresh transfer | Second: "This transfer has already been received."; stock up once |
| `IRC-02-D02` | Checks (R2) | default | qty −1; qty > sent; a `transferDetailId` of T2; a line missing; a line twice; date in future; date before transfer | Messages; nothing saved |
| `IRC-02-D03` | Units (R3) | default | TOMATO line in g: 500 | Stock +0.5 kg; stock row unit stays kg; item without a stock row at K1 gets one in kg |
| `IRC-02-D04` | Company / location (R4) | default | T3; TB; `GetTransferForReceive` TB | "Transfer not found" |
| `IRC-02-D05` | Shortage (Q1) | default | qty < sent, empty note | "Enter a reason for the shortage of …" |
| `IRC-02-D06` | Old endpoint (D5) | default | `SaveReceiveDetailsV2` with only one line | Other lines received as 0; reason not required; second call refused |
| `IRC-02-D07` | Mobile (Q4, R7) | no token; then default with `CompanyId` of CO-B in the body | `GetReceiveList`, `GetTransferDetailsForRec`, `SaveReceivedData` | 401; then body company ignored, only CO-A / U1's transfers |
| `IRC-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `IRC-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `IRC-ALL-D03` | Garbage | default | `transferId` not a GUID; `pageSize` 0 / 10000; over-posted `CompanyId` | 400 / clamped / ignored |
