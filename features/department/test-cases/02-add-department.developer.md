# 02 — Add a department — developer test cases

- Use case: [`../02-add-department.md`](../02-add-department.md) · API: [`../api-contract.md`](../api-contract.md#save-department) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Save" = `POST /AssetManagement/AddEditDepartment`; valid body `{ "departmentMasterId": "00000000-0000-0000-0000-000000000000", "departmentName": "Dev Dept" }`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `DEP-02-D01` | Authentication | no token | Save | 401; nothing written |
| `DEP-02-D02` | Authentication | `TOKEN_EXPIRED`, `TOKEN_TAMPERED` | Save | 401; nothing written |
| `DEP-02-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Save | 401 "Token does not match this site's company."; nothing written |
| `DEP-02-D04` | Main flow | default | Save | `status: true`; new row CO-A, `IsActive = 1`, `IsDelete = 0`, `CreatedBy` = A-ADMIN, `CreatedAt` in UTC |
| `DEP-02-D05` | Over-posting | default | Valid body + `"companyId": <CO-B>`, `"createdBy": "<B-ADMIN id>"`, `"isActive": false` | Row in CO-A, created by A-ADMIN, Active |
| `DEP-02-D06` | Validation / injection | default | Name "Bar@Night"; "<script>x</script>"; "x'); DROP TABLE DepartmentMaster;--" | "Department name can only contain letters, numbers, spaces and & - ( ) ' / . ,"; nothing written; table intact |
| `DEP-02-D07` | Validation | default | Name of 100; 101; 100 + surrounding spaces; missing; ""; "   " | 100 saved; 101 "Department name must be 100 characters or less"; 100 + spaces saved trimmed; missing / empty / spaces "Department name is required" |
| `DEP-02-D08` | Validation | default | Name " KITCHEN " | "Department name already exists"; nothing written |
| `DEP-02-D09` | Validation | default | Name "Old Team" (inactive) | "Department name already exists" (D3) |
| `DEP-02-D10` | Company isolation | default | Name "Sales" (CO-B only) | `status: true`; saved in CO-A |
| `DEP-02-D11` | Concurrency | default | Same body ("Race Dept") twice in parallel | Exactly one row; the other "Department name already exists" |
| `DEP-02-D12` | Permission | `TOKEN_A_VIEW` | Save | "You don't have permission to perform this action."; nothing written |
| `DEP-02-D13` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Save; `ValidateDeptName` | 401 each |
| `DEP-02-D14` | Error hygiene | default | Save while `SP_AddEditDepartment` fails | "Something went wrong. Please try again."; no SQL or stack trace |
| `DEP-02-D15` | Authentication | no token | `POST /Users/ValidateDeptName` | 401 |
| `DEP-02-D16` | Company isolation | default | Validate "Sales" | `rtrValue: false` (CO-B's name doesn't count) |
| `DEP-02-D17` | Validation / injection | default | Validate " old team " → `true`; "' OR 1=1 --" → `false`; 10 000 characters → `false` or 400, no error text | As stated |
