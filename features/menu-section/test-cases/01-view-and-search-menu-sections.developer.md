# 01 — View, search and filter — developer test cases

- Use case: [`../01-view-and-search-menu-sections.md`](../01-view-and-search-menu-sections.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "List" = `POST /Setting/GetMenuSectionWithPagination`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `MSE-01-D01` | Authentication | no token; `TOKEN_EXPIRED` | List `{}` | 401 `{ "message": "Unauthorized" }` |
| `MSE-01-D02` | Authentication | `TOKEN_TAMPERED` | List | 401 |
| `MSE-01-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | List | 401 "Token does not match this site's company." |
| `MSE-01-D04` | Main flow / isolation | default | List `{}` | `status: true`; 12 rows, active first then by name, each with `updatedAt`; no MSE-B-MAIN |
| `MSE-01-D05` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | List | 401 |
| `MSE-01-D06` | Validation / over-posting / injection | default | `{ "RecordPerPage": 1, "Pagenumber": 2, "MenuSectionName": "' OR 1=1 --", "companyId": <CO-B> }` | Paging, search and `companyId` ignored; the 12 CO-A rows; no error |
| `MSE-01-D07` | Error hygiene | default | List while the procedure fails | `status: false`, "Something went wrong. Please try again."; no SQL or stack trace |
