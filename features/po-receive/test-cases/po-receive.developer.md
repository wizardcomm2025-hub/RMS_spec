# PO Receive — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_ADMIN`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `PRC-02-D01` | **Cross-company (R1)** | default | `SaveRMSPOReceive` with `poNo` of S1 (same as PB) and no `poId`; then `poId: PB` | Attached to S1 only / "Purchase order not found" for PB; PB untouched |
| `PRC-02-D02` | Creator (R2) | U-RECV | receive S1 (created by admin); `GetPOReceiveById` of admin's receive | Allowed; returned |
| `PRC-02-D03` | Stock (R3) | default | Post ONION 500 g, damaged 100 g | Stock +0.4 kg; line `StockQty` 0.4 |
| `PRC-02-D04` | Over-receipt (R4) | default | Two drafts for S1 ONION 6 kg each; post both in parallel | One posts; the other "Only 4 kg left…"; stock +6 |
| `PRC-02-D05` | Re-post (R5) | default | Save the posted receive again; `recHeadId: RB` | "Posted receipts can't be changed." / "Receipt not found"; stock unchanged |
| `PRC-02-D06` | Validation | default | No location; future date; date before PO date; damaged > received; unit "box"; D1 / C1 / S2; empty lines; negative cost | Messages; nothing saved |
| `PRC-02-D07` | Duplicate invoice (D4) | default | Post two receives for V1 with invoice "INV-77" | Second refused |
| `PRC-02-D08` | Stale | default | Save with an old `updatedAt` | Stale message |
| `PRC-02-D09` | Over-posting | default | Extra fields `companyId`, `isDraft: false` with `post: false`, `vendorId` ≠ PO vendor | Ignored; vendor = PO vendor; stays draft |
| `PRC-02-D10` | Files (R6) | default | `fileUploads` 6 MB; `.exe`; valid pdf | Refused / refused / stored |
| `PRC-03-D01` | Delete | default | `DeletePoReceive` posted; twice on a draft; RB | Locked / ok then not found / not found |
| `PRC-ALL-D01` | Auth (R11) | none / expired / tampered / SuperAdmin | each endpoint incl. `GetPORecDetailById` | 401 |
| `PRC-ALL-D02` | Permission | U-STAFF | each endpoint | "You don't have permission to perform this action." |
| `PRC-ALL-D03` | Garbage | default | `poId: "abc"`, huge strings, double submit | 400 / validation; one receive created |
