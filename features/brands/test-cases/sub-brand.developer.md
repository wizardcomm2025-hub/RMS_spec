# Sub Brand — developer test cases

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`; routes under `/Setting/`.

| ID | Category | Caller | Request | Expected result |
|---|---|---|---|---|
| `BRD-04-D01` | Authentication / SuperAdmin | no token; `TOKEN_SA` | `GetStockSubTypeWithPagination` | 401 |
| `BRD-04-D02` | Main flow (D3) | default | List `{}` | All CO-A sub-brands incl. SB-OLD, with `stockTypeName`, `stockTypeIsActive`, `activeItemCount`, `updatedAt`; no SB-B-7UP |
| `BRD-05-D01` | Authentication (D1) | **no token** | Legacy `AddEditStockSubType`, `GetStockSubTypeById` with SB-B-7UP | 401 |
| `BRD-05-D02` | Legacy refused | default | Legacy `AddEditStockSubType` | "This endpoint is no longer available." |
| `BRD-05-D03` | Company isolation (D2) | default | `AddEditStockSubDetailsType` with SB-B-7UP's id and BRD-COKE | "Sub-brand not found"; SB-B-7UP still in CO-B (today it moves to CO-A) |
| `BRD-05-D04` | Validation | default | brand missing; brand = BRD-B; brand = BRD-OLD; name rules | "Brand is required"; "Brand not found"; "Pick an active brand."; name messages |
| `BRD-05-D05` | Business rule | default | " DIET COKE " under BRD-COKE; under BRD-FREE | Duplicate message; `status: true` |
| `BRD-05-D06` | Business rule (Q4) | default | Move SB-THUMS to BRD-FREE; move SB-DIET | Move message; `status: true` |
| `BRD-05-D07` | Over-posting | default | Add with `isActive: false, isDeleted: true, companyId: CO-B` | Active, CO-A, `IsDeleted` 0 |
| `BRD-05-D08` | Concurrency / permission | default; A-VIEW | stale token; missing token; A-VIEW add | Stale ×2; permission message |
| `BRD-05-D09` | Validate | default | `ValidateStockSubTypeName` "diet coke" with BRD-COKE / BRD-FREE; no token | true / false; 401 |
| `BRD-06-D01` | Rule bypass (Q2) | default | `ChangeStockSubTypeStatus` off SB-THUMS; on SB-OLD | Raw Materials message; "Switch its brand on first." |
| `BRD-06-D02` | Main flow | default | Off then on SB-DIET | `status: true`; still returned by the list while off |
| `BRD-06-D03` | Company isolation / delete | default | Off SB-B-7UP; `DeleteStockSubType` SB-DIET and SB-B-7UP | "Sub-brand not found"; "Sub-brands cannot be deleted. Deactivate the sub-brand instead."; unchanged |
| `BRD-06-D04` | Errors | default | Procedure failing | "Something went wrong. Please try again."; no stack trace |
