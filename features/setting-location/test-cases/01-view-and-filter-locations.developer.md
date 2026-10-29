# 01 — View, search and filter locations — developer test cases

- Use case: [`../01-view-and-filter-locations.md`](../01-view-and-filter-locations.md)
- API: [`../api-contract.md`](../api-contract.md) · response shapes: [`project-overview.md` → API conventions](../../../project-overview.md#api-conventions)
- Data model: [`../data-model.md`](../data-model.md)
- Fixtures and tokens: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "List call" = `POST /Setting/GetWareHouseDetails` with body `{ "IncludeInactive": true }` (the Location screen's call, LOC-Q5).

## Test cases

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `LOC-01-D01` | Authentication | no `Authorization` header · HOST-A | List call | 401 `{ "message": "Unauthorized" }`; no location data |
| `LOC-01-D02` | Authentication | `TOKEN_EXPIRED` · HOST-A | List call | 401 `{ "message": "Unauthorized" }` |
| `LOC-01-D03` | Authentication | `TOKEN_TAMPERED`, then `TOKEN_GARBAGE`, then `Bearer ` with empty token · HOST-A | List call | 401 `{ "message": "Unauthorized" }` each time; no 500, no stack trace |
| `LOC-01-D04` | Company isolation | `TOKEN_A_ADMIN` · HOST-A | List call | 200, `status: true`. Every row belongs to CO-A (check `WarehouseId`s against the DB). LOC-B1 and LOC-B2 are absent even though they share the Tenant DB |
| `LOC-01-D05` | Company isolation | `TOKEN_A_ADMIN` · **HOST-B** | List call | 401 `{ "status": false, "message": "Token does not match this site's company.", "data": null }` |
| `LOC-01-D06` | Company isolation | `TOKEN_A_ADMIN` · `Origin: https://unknown.example.com` | List call | 404 `{ "status": false, "message": "Site not registered.", "data": null }` |
| `LOC-01-D07` | Company isolation | `TOKEN_A_ADMIN` · **no `Origin` header** | List call | 404 "Site not registered."; no data from any company |
| `LOC-01-D08` | Over-posting | `TOKEN_A_ADMIN` · HOST-A | List call with body `{ "CompanyId": <CO-B id>, "UserId": "<B-ADMIN id>" }` | Same result as D04: CO-A rows only |
| `LOC-01-D09` | Business rule | `TOKEN_A_ADMIN` · HOST-A | List call with `IncludeInactive: true`; then with `IncludeInactive: false`; then with no body | `true`: LOC-A1, LOC-A2, LOC-A3 with `IsActive` true, true, **false**; LOC-AD (deleted) absent; active first, then name. `false` and no body: LOC-A1 and LOC-A2 only — what the other screens get; a call with no body and no `Content-Type` must still succeed (LOC-Q5) |
| `LOC-01-D10` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | List call; `GetAllLocationDDl` | 401 `{ "message": "Unauthorized" }` for both; no location or type data (LOC-Q11) |
| `LOC-01-D11` | Error hygiene | `TOKEN_A_ADMIN` · HOST-A | `POST /Setting/GetAllLocationDDl` while `SP_GetAllMap_Company_LocationType` fails (on the dev DB only: rename it temporarily, restore after) | 200 `{ "status": false, "message": "Something went wrong. Please try again." }` — **not** the bare string `"Error"` (D8); the exception is in the log4net log (LOC-Q2) |
| `LOC-01-D12` | Error hygiene | `TOKEN_A_ADMIN` · HOST-A | List call while `Sp_GetWareHouseandBinDetails` fails (as in D11) | `status: false`, message exactly "Something went wrong. Please try again."; no SQL, procedure name, stack trace or exception type (LOC-Q2) |
| `LOC-01-D13` | Authentication | no token · HOST-A | `POST /Setting/GetAllLocationDDl` | 401 `{ "message": "Unauthorized" }` |
| `LOC-01-D14` | Company isolation | `TOKEN_A_ADMIN` · HOST-A | `POST /Setting/GetAllLocationDDl` | Warehouse, Outlet, Central Kitchen only; **Commissary** (CO-B only) absent |
