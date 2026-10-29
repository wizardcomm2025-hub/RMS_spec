# 02 — Add a reason — developer test cases

- Use case: [`../02-add-reason.md`](../02-add-reason.md) · API: [`../api-contract.md`](../api-contract.md#save-reason) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Save" = `POST /Setting/AddEditResonMaster` with `reasonMasterId: 0`. "Validate" = `POST /Setting/ValidateWastageReason` (new).

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `RSN-02-D01` | Authentication | no token; `TOKEN_EXPIRED` | Save `{ "reasonMasterId": 0, "description": "Mould" }` | 401; nothing inserted |
| `RSN-02-D02` | Authentication | `TOKEN_TAMPERED` | Same | 401 |
| `RSN-02-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Same | 401 "Token does not match this site's company." |
| `RSN-02-D04` | Main flow | default | Save "Mould" | `status: true`, `data.reasonMasterId` > 0; row `CompanyId` = CO-A, `IsActive = 1`, `IsDelete = 0`, `UpdatedBy` = A-ADMIN, `UpdatedOn` ≈ UTC now, `DefaultWastageReasonId` NULL |
| `RSN-02-D05` | Over-posting | default | `{ "reasonMasterId": 0, "description": "Mould 2", "isActive": false, "companyId": <CO-B>, "updatedBy": "<other guid>" }` | Saved in CO-A, Active, `UpdatedBy` = A-ADMIN |
| `RSN-02-D06` | Validation / injection | default | `description`: missing, `null`, `""`, `"   "`, `"Spill@Bar"`, `"<script>"`, `"x'; DROP TABLE Wastage;--"`, `"Mould\u0000"` | Required message for the first four; characters message for the rest; nothing inserted |
| `RSN-02-D07` | Validation (no max) | default | 2 000-character valid text | `status: true`; stored complete (no truncation) |
| `RSN-02-D08` | Business rule | default | " EXPIRED ", "old reason" | "Reason already exists", `status: false` (today `Allready_Exit` with `true`) |
| `RSN-02-D09` | Business rule (D4) | default | "Theft" (only RSN-DEL has it) | `status: true`; new row |
| `RSN-02-D10` | Company isolation | default | "Leak" (only CO-B has it) | `status: true` |
| `RSN-02-D11` | Concurrency | `TOKEN_A_ADMIN` and `TOKEN_A_ADMIN2` | 10 parallel saves of "Race Reason" | Exactly one row; the rest "Reason already exists"; no error leaks |
| `RSN-02-D12` | Permission | `TOKEN_A_VIEW` | Save "Mould 3" | "You don't have permission to perform this action."; nothing inserted |
| `RSN-02-D13` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Save; Validate | 401 each |
| `RSN-02-D14` | Error hygiene | default | Save while `SP_AddEditResonMaster` fails | "Something went wrong. Please try again."; no SQL or stack trace |
| `RSN-02-D15` | Authentication | no token | Validate `{ "description": "Expired", "reasonMasterId": 0 }` | 401 |
| `RSN-02-D16` | Company isolation | default | Validate "Leak" (CO-B only) | `data.isExist = false` |
| `RSN-02-D17` | Validation / injection | default | Validate " expired "; "Theft" (deleted); "Expired" with `reasonMasterId` = RSN-EXPIRED; `"' OR 1=1 --"`; body `{}` | `true`; `false`; `false` (its own); `false`; `false` or 400 without stack trace |
