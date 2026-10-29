# 01 — View, search and filter vehicles — developer test cases

- Use case: [`../01-view-and-search-vehicles.md`](../01-view-and-search-vehicles.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "List" = `POST /Setting/GetVechileList` `{}`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `VEH-01-D01` | Authentication | no token | List | 401 `{ "message": "Unauthorized" }` |
| `VEH-01-D02` | Authentication | `TOKEN_EXPIRED`, `TOKEN_TAMPERED` | List | 401 each |
| `VEH-01-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B; unknown Origin | List | 401 "Token does not match this site's company."; 404 "Site not registered." |
| `VEH-01-D04` | Company isolation | default | List | 10 rows, all CO-A; `VEH-B-VAN` absent; sorted active first, then `RegNo` ASC |
| `VEH-01-D05` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | List | 401 `{ "message": "Unauthorized" }` |
| `VEH-01-D06` | Validation / over-posting / injection | default | `{ "companyId": <CO-B>, "search": "' OR 1=1 --" }` | CO-A rows only; injection text handled harmlessly as plain string; no error |
| `VEH-01-D07` | Rule bypass | default | `{ "pageNumber": 2, "pageSize": 5 }` | Still all 10 rows returned (server pagination bypassed / ignored) |
| `VEH-01-D08` | Error hygiene | default | List while `SP_GetVechileList` fails (dev DB: simulate error) | `status: false`, "Something went wrong. Please try again."; no SQL error or stack trace exposed |
