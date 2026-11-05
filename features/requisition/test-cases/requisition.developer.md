# Requisition — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_U1`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `REQ-02-D01` | Mass re-status (R1) | U-APP | Submit and approve while CO-B has approved requisitions that day | CO-B rows untouched |
| `REQ-02-D02` | Cross-company (R2) | default | `SaveRequisitionV2` with `requisitionId: RB`; old save with a CO-B line id | "Requisition not found" / line untouched |
| `REQ-02-D03` | Locked (R2) | default | save A1, T1 | "Only pending requisitions can be changed." |
| `REQ-02-D04` | Numbers (R5) | default | 20 parallel new requisitions | 20 sequential distinct numbers |
| `REQ-02-D05` | Remove line (R6) | default | save P1 without RICE | RICE line deleted |
| `REQ-02-D06` | Validation (R7) | default | K2 as From; From = To; date −1 / +31; qty 0; unit "box"; CO-B item; no lines | Messages; nothing saved |
| `REQ-03-D01` | Self-approve (R3) | default | `approve: true` / `DecideRequisition` without approve permission; `approvedBy` in body | Permission message / body ignored |
| `REQ-04-D01` | Duplicate (R4) | default | old `SaveDuplicateRequistion` onto a date+pair that has A1 | A1 still there; new pending created |
| `REQ-04-D02` | Cancel | default | cancel A1; cancel P1 twice; no reason | Locked / ok then locked / "Enter a reason" |
| `REQ-ALL-D01` | Auth | none / expired / tampered / SuperAdmin | each endpoint | 401 |
| `REQ-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `REQ-ALL-D03` | Stale / garbage | default | old `updatedAt`; bad ids; 1001 lines | Stale message / 400 / validation |
