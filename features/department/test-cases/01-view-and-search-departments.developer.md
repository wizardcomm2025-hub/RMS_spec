# 01 — View, search and filter departments — developer test cases

- Use case: [`../01-view-and-search-departments.md`](../01-view-and-search-departments.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "List" = `POST /AssetManagement/GetDepartmentList` `{}`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `DEP-01-D01` | Authentication | no token | List | 401 `{ "message": "Unauthorized" }` |
| `DEP-01-D02` | Authentication | `TOKEN_EXPIRED`, `TOKEN_TAMPERED` | List | 401 each |
| `DEP-01-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B; unknown Origin | List | 401 "Token does not match this site's company."; 404 "Site not registered." |
| `DEP-01-D04` | Company isolation | default | List | 12 rows, all CO-A; DEP-B-SALES and DEP-DEL absent; active first then by name (D1) |
| `DEP-01-D05` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | List | 401 `{ "message": "Unauthorized" }` |
| `DEP-01-D06` | Validation / over-posting / injection | default | `{ "companyId": <CO-B>, "searchName": "' OR 1=1 --", "pageNumber": -5, "pageSize": 0 }` | CO-A rows only; the search text is treated as text (no rows match, no error) |
| `DEP-01-D07` | Rule bypass | default | `{ "pageNumber": 2, "pageSize": 5 }` | Still all 12 rows (paging ignored) |
| `DEP-01-D08` | Error hygiene | default | List while `SP_GetAllDepartment` fails (dev DB: rename temporarily) | `status: false`, "Something went wrong. Please try again."; no SQL or stack trace |
