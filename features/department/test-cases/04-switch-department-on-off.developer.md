# 04 — Switch a department on or off — developer test cases

- Use case: [`../04-switch-department-on-off.md`](../04-switch-department-on-off.md) · API: [`../api-contract.md`](../api-contract.md#change-status) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Status" = `POST /AssetManagement/ChangeDepartmentStatus` (new).

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `DEP-04-D01` | Authentication | no token; `TOKEN_EXPIRED` | Status `{ "departmentMasterId": <DEP-STORE>, "isActive": false }` | 401; unchanged |
| `DEP-04-D02` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Same | 401 "Token does not match this site's company." |
| `DEP-04-D03` | Company isolation | default | Deactivate DEP-B-SALES; activate DEP-DEL | "Department not found" each; unchanged |
| `DEP-04-D04` | Main flow | default | Deactivate DEP-STORE; reactivate | `status: true`; `IsActive` 0 then 1; `IsDelete = 0`; `UpdatedBy` = A-ADMIN; `UpdatedAt` changes |
| `DEP-04-D05` | Rule bypass | default | Deactivate DEP-KITCHEN | "This department has active users. Move or deactivate them first."; unchanged |
| `DEP-04-D06` | Rule bypass | default | Deactivate DEP-SERVICE | "This department is used by active roster patterns. Deactivate them first."; unchanged |
| `DEP-04-D07` | Business rule | default | Deactivate DEP-BAR (only inactive user / pattern) | `status: true`; Inactive |
| `DEP-04-D08` | Validation | default | `isActive` missing / null / "maybe"; `departmentMasterId` missing / "abc" / empty GUID | 400 or `status: false` ("Department not found" for GUIDs) without stack trace; nothing changes |
| `DEP-04-D09` | Over-posting | default | `{ departmentMasterId: DEP-STORE, isActive: false, "companyId": <CO-B>, "isDelete": true }` | Deactivated in CO-A; `IsDelete` stays 0; `UpdatedBy` = A-ADMIN |
| `DEP-04-D10` | Permission | `TOKEN_A_VIEW` | Deactivate DEP-STORE | "You don't have permission to perform this action."; unchanged |
| `DEP-04-D11` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Status; `DeleteDepartment` | 401 each |
| `DEP-04-D12` | Rule bypass (delete) | default | `POST /AssetManagement/DeleteDepartment` `{ "departmentMasterId": <DEP-STORE> }` | "Departments cannot be deleted. Deactivate the department instead."; unchanged (today it is deleted) |
| `DEP-04-D13` | Company isolation | default | `DeleteDepartment` with DEP-B-SALES | Same refusal; unchanged |
| `DEP-04-D14` | Concurrency | default | Deactivate and activate DEP-STORE in parallel, 10 rounds | Every round ends in one valid state (`IsActive` 0 or 1, `IsDelete = 0`); no error leaks |
| `DEP-04-D15` | Error hygiene | default | Status while `SP_ChangeDepartmentStatus` fails | "Something went wrong. Please try again."; no SQL or stack trace |
