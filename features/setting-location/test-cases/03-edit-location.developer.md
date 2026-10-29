# 03 — Edit a location — developer test cases

- Use case: [`../03-edit-location.md`](../03-edit-location.md)
- API: [`../api-contract.md`](../api-contract.md) · response shapes: [`project-overview.md` → API conventions](../../../project-overview.md#api-conventions)
- Data model: [`../data-model.md`](../data-model.md)
- Fixtures and tokens: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Get" = `POST /Setting/GetWareHouseDetailsById`. "Save" = `POST /Setting/SaveWareHouseDetails` with the location's current values **including `UpdatedAt`** (from a fresh Get) unless the case changes a field.

## Test cases

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `LOC-03-D01` | Authentication | no token · HOST-A | Get `{ "WarehouseId": <LOC-A1> }` | 401 `{ "message": "Unauthorized" }`; no location data (D3 — today it returns data) |
| `LOC-03-D02` | Validation | default | Get `{ "WarehouseId": 999999 }` (unknown) | 200 `{ "status": false, "message": "Location not found" }`; no exception text (today: index error) |
| `LOC-03-D03` | Company isolation | default | Get `{ "WarehouseId": <LOC-B1> }` | `status: false`, "Location not found"; **no** CO-B name, address or employees in the body |
| `LOC-03-D04` | Validation | default | Get `{ "WarehouseId": <LOC-AD> }` (deleted) | `status: false`, "Location not found" |
| `LOC-03-D05` | Validation / injection | default | Get with `WarehouseId` 0, -1, 9223372036854775807, "abc", "1 OR 1=1", and no body | `status: false` "Location not found" for the numbers; 400 or `status: false` for the rest; never a stack trace, never another location's data |
| `LOC-03-D06` | Over-posting / isolation | default | Get `{ "WarehouseId": <LOC-B1>, "CompanyId": <CO-B id>, "UserId": "<B-ADMIN id>" }` | Same as D03 — body `CompanyId` / `UserId` ignored |
| `LOC-03-D07` | Contract | default | Get LOC-A3 | `status: true`; `Data.IsActive = false`; other fields match the DB |
| `LOC-03-D08` | Company isolation | `TOKEN_A_ADMIN` · **HOST-B** | Get LOC-A1 | 401 "Token does not match this site's company." |
| `LOC-03-D09` | Validation (duplicate) | default | Save LOC-A2 with `WarehouseName` "Kitchen" | `status: false`, "Location name already exists" (D2 — today `status: true`, `Response: "Exist"`). LOC-A2 row unchanged |
| `LOC-03-D10` | Company isolation | default | Save with `WarehouseId` = LOC-B1, LOC-B1's `UpdatedAt` and name "Hijacked" | `status: false`, "Location not found"; LOC-B1 unchanged (name, flags, `UpdatedBy`); no CO-A row created or changed; nothing about LOC-B1 in the body (LOC-Q9) |
| `LOC-03-D11` | Rule bypass | default | Save with `WarehouseId` = LOC-AD (deleted), name "Revived" | `status: false`, "Location not found"; LOC-AD stays `IsDeleted = 1` with its old name (LOC-Q9) |
| `LOC-03-D12` | Ownership | `TOKEN_A_ADMIN2` · HOST-A | Save LOC-A1 with City "Thane" | `status: true`. `UserResponsible` and `CreatedBy` still A-ADMIN; `UpdatedBy` = A-ADMIN2; `UpdatedAt` set (D5) |
| `LOC-03-D13` | Rule bypass (bins) | default | Save LOC-A1 with `BinNumbers: []`; then with `BinNumbers: [{ "BinNumberId": 0, "BinNumber": "ZZ" }]` | LOC-A1's 2 `BinWarehouse` rows unchanged (`IsActive` and values), no new bin (D4) |
| `LOC-03-D14` | Over-posting | default | Save LOC-A1 with `"UserResponsible"`, `"CreatedBy"` = EMP-A2's id, `"CompanyId"` = CO-B | Ignored: owner fields unchanged; `CompanyId` stays CO-A |
| `LOC-03-D15` | Rule bypass | default | Save LOC-A1 with `"IsActive": false, "IsDeleted": true` | Ignored: LOC-A1 stays active and not deleted. Status changes only through `ChangeWareHouseStatus` |
| `LOC-03-D16` | Rule bypass (POS) | default | Save LOC-A1 (any field) | LOC-A1 `IsPOSOutlet = 1`; LOC-A2 and every other location's POS flag unchanged (TC-2, D1) |
| `LOC-03-D17` | Concurrency | A-ADMIN and A-ADMIN2 | Both Get LOC-A2 (same `UpdatedAt`). A-ADMIN saves City "One"; then A-ADMIN2 saves City "Two" with the old `UpdatedAt`. Repeat with both saves sent in parallel | Sequential: first `status: true`; second `status: false`, "This location was changed by someone else. Reload and try again."; City stays "One". Parallel: exactly one succeeds, the other gets the stale message; one consistent row (LOC-Q7) |
| `LOC-03-D18` | Permission | `TOKEN_A_VIEW` · HOST-A | Save LOC-A2 with City "NoPerm" and its current `UpdatedAt` | `status: false`, "You don't have permission to perform this action."; row unchanged (LOC-Q1) |
| `LOC-03-D19` | Validation | default | Save LOC-A1 with name " kitchen " | `status: true`; stored as "kitchen" (trimmed); no duplicate error against itself |
| `LOC-03-D20` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Get LOC-A1; Save LOC-A1 | 401 `{ "message": "Unauthorized" }` each; nothing returned or written (LOC-Q11) |
| `LOC-03-D21` | Concurrency (bypass) | default | Save LOC-A2 with a valid body but **no** `UpdatedAt` field; then `"UpdatedAt": null` | `status: false`, "This location was changed by someone else. Reload and try again."; row unchanged (LOC-Q7) |
| `LOC-03-D22` | Concurrency | default | Get LOC-A2; deactivate it with `ChangeWareHouseStatus`; Save LOC-A2 with the `UpdatedAt` from the Get | `status: false`, "This location was changed by someone else. Reload and try again."; LOC-A2 stays inactive with its old values |
| `LOC-03-D23` | Business rule | default | Save LOC-A2 resending its existing employees, including EMP-AX (now inactive, mapped before) | `status: true`; the EMP-AX mapping is kept; no "employees are not valid" error (LOC-Q6, ui-ux §3) |
| `LOC-03-D24` | Contract | default | Get LOC-A2 | `Data.UpdatedAt` present and never null (falls back to `CreatedAt`); after any save or status change it differs from the earlier value |
