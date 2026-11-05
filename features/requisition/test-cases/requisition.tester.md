# Requisition — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `REQ-01-T01` | 01 | Open Stock → Requisition as U1. | This month's requisitions from K1; paging footer. |
| `REQ-01-T02` | 01 | Search part of P1's number; Status Approved. | Matching rows only; Clear filters resets. |
| `REQ-02-T01` | 02 | New requisition: today, K1 → CS; ONION 5 kg, RICE 10; Submit. | "Requisition REQ/2026-27/0001 submitted"; Pending. |
| `REQ-02-T02` | 02 | Edit it: clear RICE, ONION 6; Submit. | RICE gone; ONION 6 kg. |
| `REQ-02-T03` | 02 | From K1, To K1. | To list doesn't offer K1. |
| `REQ-02-T04` | 02 | As U1 (no approve). | "Submit and approve" not shown. |
| `REQ-02-T05` | 02 | Open A1's edit URL. | Read-only message; can't save. |
| `REQ-03-T01` | 03 | As U-APP open P1 → Approve. | Confirm; "Requisition approved"; P1 in Inbound Requisition at CS. |
| `REQ-03-T02` | 03 | Decline without a note; with "Not needed". | Note required; then "Requisition declined". |
| `REQ-04-T01` | 04 | Cancel P1 with reason. | Status Cancelled; no Edit / Cancel actions. |
| `REQ-04-T02` | 04 | Duplicate A1 for tomorrow. | New pending requisition with A1's lines; A1 unchanged. |
| `REQ-UI-T01` | UI | List and create page at 360 px. | Phone cards; fields stack; no page sideways scroll. |
