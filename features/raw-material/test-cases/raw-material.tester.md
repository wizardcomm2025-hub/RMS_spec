# Raw Material — tester test cases

| ID | Covers | Steps | Expected result |
|---|---|---|---|
| `RAW-01-T01` | 01 | Open Raw Material; rows per page 10; page 2; filter Category C-RAW; Status Inactive. | Footer "1–10 of N raw materials"; results from the server; RM-OFF shows Inactive; Clear filters resets. |
| `RAW-01-T02` | 01 | Look at RM-NEW and RM-USED rows. | Delete icon on RM-NEW only. |
| `RAW-02-T01` | 02 | Add "Tomato (Local)" with cost 12.50, two locations, vendors V1 (default) and V2. | "Raw material created"; edit shows cost 12.50, both locations and vendors, V1 default. |
| `RAW-02-T02` | 02 | Edit it: remove V2, type a Reference No and SKU, save; reopen. | V2 is gone; Reference No and SKU are kept. |
| `RAW-02-T03` | 02 | Add a raw material named like an existing Finished Goods item. | "Raw material name already exists (Finished Goods)". |
| `RAW-02-T04` | 02 | Edit RM-USED. | Main unit read-only with "Can't change: already in use"; alt unit editable. |
| `RAW-03-T01` | 03 | Turn off RM-NEW → Deactivate; open the recipe ingredient picker. | "Raw material deactivated"; RM-NEW not offered. |
| `RAW-04-T01` | 04 | Delete RM-NEW → Delete. | "Raw material deleted"; gone from the list. |
| `RAW-02-T05` | 02 | Purchase Order → OCR → create a raw material from a scanned line. | Dialog opens prefilled; saves; appears in the list. |
| `RAW-UI-T01` | UI | As U-STAFF; at 375 px. | No Add / Edit / Delete / switch; phone cards. |
