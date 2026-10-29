# 01 — View, search and filter reasons — developer test cases

- Use case: [`../01-view-and-search-reasons.md`](../01-view-and-search-reasons.md) · API: [`../api-contract.md`](../api-contract.md#list-reasons) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "List" = `POST /Setting/GetWarehouseReason`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `RSN-01-D01` | Authentication | no token; `TOKEN_EXPIRED` | List `{ "openMode": "All" }` | 401 `{ "message": "Unauthorized" }` |
| `RSN-01-D02` | Authentication | `TOKEN_TAMPERED` | Same | 401 |
| `RSN-01-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Same | 401 "Token does not match this site's company." |
| `RSN-01-D04` | Main flow / isolation | default | List `{ "openMode": "All" }` | `status: true`; 12 rows, active first then A–Z; no RSN-DEL, no RSN-B-LEAK; `totalRecords = 12` |
| `RSN-01-D05` | Rule bypass | default | List `{ "openMode": "Active" }`; `{ "openMode": "active" }`; `{ "openMode": "x" }` | `Active` → 11 rows, no Old Reason; unknown value → treated as `All` (12) |
| `RSN-01-D06` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | List | 401 |
| `RSN-01-D07` | Validation / over-posting / injection | default | `{ "openMode": "All", "pageNumber": 2, "pageSize": 1 }`; `{ "searchText": "' OR 1=1 --" }`; `{ "searchText": "  exp " }`; `{ "companyId": <CO-B> }` | Paging ignored (12 rows); injection text → 0 rows, no error; trimmed search → Expired; `companyId` ignored (CO-A rows) |
| `RSN-01-D08` | Error hygiene | default | List while `SP_GetWarehouseReason` fails (renamed in a test DB) | `status: false`, "Something went wrong. Please try again."; no SQL or stack trace |
