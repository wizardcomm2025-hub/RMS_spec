# 01 — View and search drivers — developer test cases

- Use case: [`../01-view-and-search-drivers.md`](../01-view-and-search-drivers.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "List" = `POST /Setting/GetDriverList`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `DRV-01-D01` | Authentication | no token; `TOKEN_EXPIRED` | List `{}` | 401 `{ "message": "Unauthorized" }` |
| `DRV-01-D02` | Authentication | `TOKEN_TAMPERED` | List | 401 |
| `DRV-01-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | List | 401 "Token does not match this site's company." |
| `DRV-01-D04` | Main flow / isolation | default | List `{}` | `status: true`; DRV_1, DRV_2, DRV_3 (active first, then by name), each with `lastUpdatedOn`; no DRV_B |
| `DRV-01-D05` | Business rule (dispatch) | default | List `{ "activeOnly": true }` | Only DRV_1, DRV_2; DRV_3 (inactive) absent |
| `DRV-01-D06` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | List | 401 |
| `DRV-01-D07` | Validation / over-posting | default | `{ "activeOnly": "maybe" }`; `{ "companyId": <CO-B> }`; `{ "query": "'; DROP TABLE DriverMaster; --" }` | 400 or full CO-A list without stack trace; `companyId` and unknown fields ignored; table intact |
| `DRV-01-D08` | Error hygiene | default | List while `GetDriverList` fails | `status: false`, "Something went wrong. Please try again."; no SQL or stack trace |
