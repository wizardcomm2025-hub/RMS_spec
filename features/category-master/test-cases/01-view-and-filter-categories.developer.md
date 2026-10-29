# 01 — View, search and filter categories — developer test cases

- Use case: [`../01-view-and-filter-categories.md`](../01-view-and-filter-categories.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "List" = `POST /Setting/GetStockGroupList`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `CAT-01-D01` | Authentication | no token · HOST-A | List; `GetInventoryTypeCompanyWise` | 401 `{ "message": "Unauthorized" }` each |
| `CAT-01-D02` | Authentication | `TOKEN_EXPIRED` · HOST-A | List | 401 |
| `CAT-01-D03` | Authentication | `TOKEN_TAMPERED` · HOST-A | List | 401; no stack trace |
| `CAT-01-D04` | Company isolation | default | List | 200, `status: true`; every row has CO-A's `CompanyId`; CAT-B-VEG and CAT-B-SPICE absent although they share the Tenant DB |
| `CAT-01-D05` | Company isolation | `TOKEN_A_ADMIN` · **HOST-B** | List | 401 "Token does not match this site's company." |
| `CAT-01-D06` | Company isolation | `TOKEN_A_ADMIN` · unknown Origin | List | 404 "Site not registered." |
| `CAT-01-D07` | Business rule | default | List | CAT-OLD and CAT-OLDSUB present with `isActive: false`; CAT-DEL absent; others `isActive: true` |
| `CAT-01-D08` | Business rule | default | List | ASSET-A (`ClassificationId = 2`) absent; every row has `classificationId: 1` |
| `CAT-01-D09` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | List; `GetInventoryTypeCompanyWise` | 401 `{ "message": "Unauthorized" }` each |
| `CAT-01-D10` | Error hygiene | default | List while `SP_GetStockGroupList` fails (dev DB only: rename temporarily, restore after) | `status: false`, message exactly "Something went wrong. Please try again."; no SQL, procedure name or stack trace |
| `CAT-01-D11` | Company isolation | default | `GetInventoryTypeCompanyWise` | Raw Material and Finished Goods only; CO-B's Packaging absent |
