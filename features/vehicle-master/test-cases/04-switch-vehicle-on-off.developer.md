# 04 — Switch a vehicle on or off — developer test cases

- Use case: [`../04-switch-vehicle-on-off.md`](../04-switch-vehicle-on-off.md) · API: [`../api-contract.md`](../api-contract.md#change-status) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Status" = `POST /Setting/ChangeVehicleStatus`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `VEH-04-D01` | Authentication | no token; `TOKEN_EXPIRED` | Status `{ "vehicleId": <VEH-VAN-1>, "isActive": false }` | 401; record unchanged |
| `VEH-04-D02` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Same | 401 "Token does not match this site's company." |
| `VEH-04-D03` | Company isolation | default | Deactivate `VEH-B-VAN` (CO-B vehicle) | `status: false`, "Vehicle not found"; CO-B row unchanged |
| `VEH-04-D04` | Main flow | default | Deactivate `VEH-VAN-1`; then reactivate | `status: true`; `IsActive` toggles 0 then 1; `LastUpdatedBy` = A-ADMIN; `LastUpdatedOn` updated in UTC |
| `VEH-04-D05` | Idempotency | default | Deactivate `VEH-OLD` (already inactive) | `status: true`; remains `IsActive = 0` |
| `VEH-04-D06` | Validation | default | `vehicleId` missing, empty GUID, "123", or invalid format | 400 or `status: false` ("Vehicle not found" for empty GUID) without stack trace; record unchanged |
| `VEH-04-D07` | Validation | default | `isActive` missing, null, or invalid type | 400 Bad Request; nothing updated |
| `VEH-04-D08` | Over-posting | default | `{ "vehicleId": <VEH-VAN-1>, "isActive": false, "companyId": <CO-B> }` | Deactivated in CO-A; `CompanyId` remains CO-A; `LastUpdatedBy` = A-ADMIN |
| `VEH-04-D09` | Permission | `TOKEN_A_VIEW` | Deactivate `VEH-VAN-1` | "You don't have permission to perform this action."; unchanged |
| `VEH-04-D10` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Status; `DeleteVehicle` | 401 each |
| `VEH-04-D11` | Rule bypass (delete blocked) | default | `POST /Setting/DeleteVehicle` `{ "vehicleId": <VEH-VAN-1> }` | 200, `status: false`, "Vehicles cannot be deleted. Deactivate the vehicle instead."; row remains in DB (D1) |
| `VEH-04-D12` | Company isolation (delete) | default | `DeleteVehicle` with `VEH-B-VAN` | Same refusal message; CO-B row remains intact |
| `VEH-04-D13` | Concurrency | default | Deactivate and activate `VEH-VAN-1` in parallel, 10 rounds | Ends in a consistent state (`IsActive` 0 or 1); no database deadlock or unhandled exception |
| `VEH-04-D14` | Error hygiene | default | Status while `SP_ChangeVehicleStatus` fails | "Something went wrong. Please try again."; no SQL error or internal details exposed |
