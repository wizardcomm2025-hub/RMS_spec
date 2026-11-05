# PO Receive — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `PRC-01-T01` | 01 | Open Purchase → PO Receive. | This month's receipts, newest first; footer "1–n of n receipts". |
| `PRC-01-T02` | 01 | Filter Status Posted, Vendor V1; search "GRN/2026". | Only matching rows; Clear filters resets. |
| `PRC-01-T03` | 01 | Date range: pick only a start date, close. | List doesn't reload; Apply with one day filters that day. |
| `PRC-02-T01` | 02 | ＋ Receive goods → open the PO picker. | S1 listed; S2, D1, C1 not listed. |
| `PRC-02-T02` | 02 | Pick S1. | Vendor V1 shown; lines ONION 10 / 0 / 10, OIL 5 / 0 / 5; Receiving prefilled 10 and 5. |
| `PRC-02-T03` | 02 | ONION Receiving 6, Damaged 1; L1; Save draft. | "Receipt saved as draft (GRN/2026-27/0001)"; stock unchanged. |
| `PRC-02-T04` | 02 | Open the draft, Post receipt, confirm. | Confirm text names L1; "Receipt posted (GRN/2026-27/0001)"; ONION stock at L1 +5 kg. |
| `PRC-02-T05` | 02 | New receive for S1: ONION Receiving 5. | Inline "Only 4 kg left on the purchase order for Onion"; Post blocked. |
| `PRC-02-T06` | 02 | No location; Post. | "Select a location" under the field. |
| `PRC-02-T07` | 02 | Change Receive total to a different value. | Caption "Differs from lines by …"; saves. |
| `PRC-02-T08` | 02 | Attach invoice.pdf (1 MB), save, reopen. | File shown as a link. |
| `PRC-02-T09` | 02 | Same invoice no as an earlier V1 receive; Post. | "Invoice … is already recorded for this vendor". |
| `PRC-02-T10` | 02 | Open a posted receive. | Read-only text; footer "Back to list". |
| `PRC-03-T01` | 03 | Delete a draft. | "Delete receipt GRN/…?" → "Receipt deleted"; gone; stock unchanged. |
| `PRC-04-T01` | 04 | PO list after PRC-02-T04. | S1 chip Submitted with "Partly received". |
| `PRC-UI-T01` | UI | List and form at 360 px. | Phone cards; form fields stack; no page sideways scroll. |
