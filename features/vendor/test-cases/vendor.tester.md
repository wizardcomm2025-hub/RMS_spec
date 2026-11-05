# Vendor — tester test cases

| ID | Covers | Steps | Expected result |
|---|---|---|---|
| `VND-01-T01` | 01 | Open Vendor; search a city; Status Inactive; Clear filters. | Each vendor once; V-OFF Inactive; filters work. |
| `VND-01-T02` | 01 | Hover Delete on V-USED; look at V-NEW. | V-USED disabled with the reason; V-NEW enabled. |
| `VND-02-T01` | 02 | Add "A1 Traders & Co." with phone only. | "Vendor created". |
| `VND-02-T02` | 02 | Add with GSTIN "27abcde1234f1z5"; then another with the same GSTIN. | First saved as 27ABCDE1234F1Z5; second "GST number already exists for …". |
| `VND-02-T03` | 02 | GSTIN "12345"; phone "98765". | GSTIN format message; phone must be 10 digits. |
| `VND-02-T04` | 02, Q4 | Save while Tally is unreachable. | Saved; warning "Vendor saved. Couldn't send it to Tally …". |
| `VND-03-T01` | 03 | Turn off V-NEW; open a new PO. | "Vendor deactivated"; not offered on the PO. |
| `VND-04-T01` | 04 | Delete V-NEW. | "Vendor deleted"; gone. |
| `VND-UI-T01` | UI | As U-STAFF; at 375 px. | No Add / Edit / Delete / switch; phone cards. |
