# 04 — Switch a location on or off — developer test cases

- Use case: [`../04-switch-location-on-off.md`](../04-switch-location-on-off.md)
- API: [`../api-contract.md`](../api-contract.md#change-status) · response shapes: [`project-overview.md` → API conventions](../../../project-overview.md#api-conventions)
- Data model: [`../data-model.md`](../data-model.md)
- Fixtures and tokens: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Status" = `POST /Setting/ChangeWareHouseStatus` (new endpoint — every case fails until the API phase lands).

## Test cases

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `LOC-04-D01` | Authentication | no token · HOST-A | Status `{ "warehouseId": <LOC-A2>, "isActive": false }` | 401 `{ "message": "Unauthorized" }`; LOC-A2 unchanged |
| `LOC-04-D02` | Authentication | `TOKEN_TAMPERED`, `TOKEN_EXPIRED` · HOST-A | Same | 401; unchanged |
| `LOC-04-D03` | Company isolation | `TOKEN_A_ADMIN` · **HOST-B** | Same | 401 "Token does not match this site's company."; unchanged |
| `LOC-04-D04` | Company isolation | default | `{ "warehouseId": <LOC-B1>, "isActive": false }` | `status: false`, "Location not found"; LOC-B1 still active and Default |
| `LOC-04-D05` | Validation | default | `warehouseId` 999999 | `status: false`, "Location not found" |
| `LOC-04-D06` | Rule bypass | default | `{ "warehouseId": <LOC-AD>, "isActive": true }` (deleted) | `status: false`, "Location not found"; LOC-AD stays `IsDeleted = 1, IsActive = 0` |
| `LOC-04-D07` | Validation | default | `isActive` missing, `null`, `"maybe"`, `2` | Rejected (400 or `status: false`) with no stack trace; nothing changes |
| `LOC-04-D08` | Validation | default | `warehouseId` missing, 0, -1, "abc", "1; DROP TABLE Warehouse" | `status: false` "Location not found" or 400; nothing changes; table intact |
| `LOC-04-D09` | Business rule | default | `{ "warehouseId": <LOC-A1>, "isActive": false }` (LOC-A1 is Default) | `status: true`. LOC-A1: `IsActive = 0`, `IsDeleted = 0`, `IsDefault = 0`, `UpdatedBy` = A-ADMIN. `UserWarehouseMapping` and `BinWarehouse` rows of LOC-A1 unchanged |
| `LOC-04-D10` | Business rule | default | After D09: `{ "warehouseId": <LOC-A1>, "isActive": true }` | `status: true`. `IsActive = 1`, `IsDefault` stays 0; mappings identical to before D09 |
| `LOC-04-D11` | Idempotency | default | Deactivate LOC-A3 (already inactive) twice | `status: true` both times (or a clear no-op result); row unchanged apart from `UpdatedBy/At` |
| `LOC-04-D12` | Concurrency | default | Send `isActive: false` and `isActive: true` for LOC-A2 in parallel, 10 rounds | Every round ends with LOC-A2 in one valid state (`IsDeleted = 0`, `IsDefault` never 1 while inactive); no error leaks |
| `LOC-04-D13` | Over-posting | default | `{ "warehouseId": <LOC-A2>, "isActive": false, "CompanyId": <CO-B id>, "UserId": "<B-ADMIN id>", "IsDeleted": true }` | LOC-A2 deactivated; `IsDeleted` stays 0; `UpdatedBy` = A-ADMIN; nothing in CO-B changes |
| `LOC-04-D14` | Permission | `TOKEN_A_VIEW` · HOST-A | Deactivate LOC-A2 | `status: false`, "You don't have permission to perform this action."; unchanged (LOC-Q1) |
| `LOC-04-D15` | Rule bypass (delete) | default | `POST /Setting/DeleteWareHouseDetails` `{ "WarehouseId": <LOC-A2> }` | `status: false`, "Locations cannot be deleted. Deactivate the location instead."; LOC-A2 unchanged, its bins and employees untouched (LOC-Q8). Today the call sets `IsDeleted = 1` |
| `LOC-04-D16` | Company isolation | default | `POST /Setting/DeleteWareHouseDetails` `{ "WarehouseId": <LOC-B1> }` | Same message as D15; LOC-B1 unchanged and its bins untouched |
| `LOC-04-D17` | Error hygiene | default | Status while `Sp_ChangeWareHouseStatus` fails (dev DB: rename temporarily) | `status: false`, message exactly "Something went wrong. Please try again."; no SQL or stack trace (LOC-Q2) |
| `LOC-04-D18` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Deactivate LOC-A2; call `DeleteWareHouseDetails` for LOC-A2 | 401 `{ "message": "Unauthorized" }` each; unchanged (LOC-Q11) |
