# 04 — Switch a driver on or off — developer test cases

- Use case: [`../04-switch-driver-on-off.md`](../04-switch-driver-on-off.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Status" = `POST /Setting/ChangeDriverStatus` (new).

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `DRV-04-D01` | Authentication | no token; `TOKEN_EXPIRED` | Status `{ "driverId": <DRV_2>, "isActive": false }` | 401; unchanged |
| `DRV-04-D02` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Same | 401 "Token does not match this site's company." |
| `DRV-04-D03` | Company isolation | default | Deactivate DRV_B; unknown GUID | "Driver not found" each; unchanged |
| `DRV-04-D04` | Main flow | default | Deactivate DRV_2; reactivate | `status: true`; `IsActive` 0 then 1; `LastUpdatedBy` = A-ADMIN; `LastUpdatedOn` changes; name, licence, photo untouched |
| `DRV-04-D05` | Business rule (Q5) | default | Deactivate DRV_2; List `{ "activeOnly": true }` | DRV_2 absent; past transfers that used DRV_2 still show it |
| `DRV-04-D06` | Validation | default | `isActive` missing / "maybe"; `driverId` missing / "abc" | 400 or `status: false` without stack trace; nothing changes |
| `DRV-04-D07` | Over-posting | default | `{ driverId: DRV_2, isActive: false, "driverName": "X", "companyId": <CO-B> }` | Deactivated in CO-A; name unchanged |
| `DRV-04-D08` | Permission | `TOKEN_A_VIEW` | Deactivate DRV_2 | "You don't have permission to perform this action."; unchanged |
| `DRV-04-D09` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Status; `DeleteDriver` | 401 each |
| `DRV-04-D10` | Rule bypass (delete) | default | `POST /Setting/DeleteDriver` `{ "driverId": <DRV_2> }` | "Drivers cannot be deleted. Deactivate the driver instead."; row still there (today it is deleted) |
| `DRV-04-D11` | Concurrency | default | Deactivate and activate DRV_2 in parallel, 10 rounds | Each round ends in one valid state; no error leaks |
| `DRV-04-D12` | Error hygiene | default | Status while `SP_ChangeDriverStatus` fails | "Something went wrong. Please try again."; no SQL or stack trace |
