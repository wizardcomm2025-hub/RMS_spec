# 03 — Rename a department — developer test cases

- Use case: [`../03-rename-department.md`](../03-rename-department.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Get" = `POST /AssetManagement/GetDepartmentById`. "Save" = `AddEditDepartment` with the department's id, a new name and **`updatedAt` from a fresh Get**.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `DEP-03-D01` | Authentication | no token | Get DEP-STORE | 401 |
| `DEP-03-D02` | Company isolation | default | Get DEP-B-SALES | `status: false`, "Department not found"; no CO-B data |
| `DEP-03-D03` | Over-posting / isolation | default | Get DEP-B-SALES with `"companyId": <CO-B>` | "Department not found" |
| `DEP-03-D04` | Rule bypass | default | Get DEP-DEL (deleted) | "Department not found" (D4 — today it returns the row) |
| `DEP-03-D05` | Validation / error hygiene | default | Get with random GUID, empty GUID, "abc", no body; then Get while `SP_GetDepartmentById` fails | "Department not found" or 400 without stack trace; failure → "Something went wrong. Please try again." |
| `DEP-03-D06` | Validation | default | Save DEP-STORE as "Kitchen" | "Department name already exists"; unchanged |
| `DEP-03-D07` | Company isolation | default | Save with DEP-B-SALES's id and name "Hijacked" | "Department not found"; CO-B row unchanged (D4 — today reports success) |
| `DEP-03-D08` | Rule bypass | default | Save DEP-STORE (active) with `"isActive": false` | `status: true`; still Active (status changes only through `ChangeDepartmentStatus`) |
| `DEP-03-D09` | Concurrency | A-ADMIN, A-ADMIN2 | Both Get DEP-STORE; first saves; second saves with the old `updatedAt`. Repeat in parallel | Second: "This department was changed by someone else. Reload and try again."; parallel: exactly one succeeds |
| `DEP-03-D10` | Concurrency (bypass) | default | Save DEP-STORE without `updatedAt`; with `null`; after a status change | Changed-by-someone-else message each; unchanged |
| `DEP-03-D11` | Permission | `TOKEN_A_VIEW` | Save DEP-STORE | "You don't have permission to perform this action."; unchanged |
| `DEP-03-D12` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Get; Save | 401 each |
