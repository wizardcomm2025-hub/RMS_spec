# Purchase Order — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_ADMIN`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `PO-02-D01` | **Takeover (D1)** | default | `SavePODetails` with `poId: PB` | "Purchase order not found"; PB unchanged |
| `PO-02-D02` | Line of another PO (D2) | default | save D1 with a line `poDetailsId: LB` | LB unchanged; treated as a new line of D1 |
| `PO-02-D03` | Locked (D3) | default | save S1; `status: "Received"` on D1 | Locked message; invalid status refused |
| `PO-02-D04` | Numbers (D4) | default | 20 parallel new POs | 20 distinct sequential numbers |
| `PO-02-D05` | Double line delete (D5) | default | `DeletePoDetailsById` same line twice | Total reduced once; second call not found |
| `PO-02-D06` | Validation (D6, D7) | default | V-OFF; RM-OFF; CO-B item; qty 0; cost -1; RL1 already on S1; no lines | Messages; nothing saved |
| `PO-02-D07` | Concurrency | default | stale / missing `updatedAt` | Stale message |
| `PO-02-D08` | Permission (D8) | `TOKEN_U_STAFF` | save; cancel; delete | Permission message |
| `PO-03-D01` | Cancel | default | cancel S1 (no reason); S1 (reason); S2; D1; PB | Reason message; ok; received message; locked; not found |
| `PO-04-D01` | Delete draft | default | delete D1; S1; PB | ok; locked; not found |
| `PO-01-D01` | Cross-company read | default | `GetGetPurchaseOrderDetailsbyId` PB | Not found |
| `PO-01-D02` | Paging | default | `GetPurchaseOrderList` page 2 size 10 with filters | Exact total; only CO-A |
| `PO-02-D09` | Auth | no token; `TOKEN_SA` | every endpoint above | 401 |

## 05 — Email to vendor
| ID | Category | Request | Expected |
|---|---|---|---|
| `PO-05-D01` | Auth | No / expired / tampered / SuperAdmin token on both endpoints | 401 |
| `PO-05-D02` | Company | `GetPoEmailDefaults` / `EmailPurchaseOrder` with CO-B's `poId` | "Purchase order not found"; nothing sent or logged |
| `PO-05-D03` | Status | Draft or Cancelled PO | "Only submitted purchase orders can be emailed." |
| `PO-05-D04` | Permission | U-VIEW (no `create_purchase_order`) | "You don't have permission to perform this action." |
| `PO-05-D05` | Garbage | `toEmails` empty; 11 addresses; `"a@b"`; subject 201 chars; message 2001 chars | The matching messages; nothing sent |
| `PO-05-D06` | Injection | message `<script>alert(1)</script>` | Arrives as text, not HTML |
| `PO-05-D07` | Over-posting | body with `fromEmail`, `companyId`, `attachmentHtml` | Ignored; PDF built from the saved PO |
| `PO-05-D08` | Mail not set up | Central `PlatformNotificationSecret` EmailTemplate inactive | "Email isn't set up yet. Contact support."; log row `IsSuccess = 0` |
| `PO-05-D09` | SMTP failure | wrong SMTP password | "Couldn't send the email. Please try again."; log row with `ErrorMessage` |
| `PO-05-D10` | No change | send twice | Two log rows; `PurchaseOrderMaster.UpdatedAt` and status unchanged |
