# Inbound Requisition — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `INB-01-T01` | 01 | Open Stock → Inbound Requisition as U1. | A1 listed, Left to send "1 of 1"; P1 not listed. |
| `INB-02-T01` | 02 | Dispatch A1. | ONION approved 6, sent 0, remaining 6, in stock 10, Send prefilled 6. |
| `INB-02-T02` | 02 | Send 4, V1, D1; Dispatch; confirm. | "Transfer TRF/2026-27/0001 dispatched"; print dialog; CS stock 6 kg; A1 "1 of 1" left (2 kg). |
| `INB-02-T03` | 02 | Dispatch A1 again; Send 3. | Red outline; "Only 2 kg of Onion left to send". |
| `INB-02-T04` | 02 | No vehicle. | "Select a vehicle". |
| `INB-02-T05` | 02 | As K1's user open Item Receive. | TRF/…/0001 is there (sender CS). |
| `INB-02-T06` | 02 | Send all of A1. | A1 shows "Fully sent" under Show: Fully sent. |
| `INB-UI-T01` | UI | List and dispatch at 360 px. | Phone cards; fields stack; no page sideways scroll. |
