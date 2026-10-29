# 03 — Edit a vehicle — developer test cases

- Use case: [`../03-edit-vehicle.md`](../03-edit-vehicle.md) · API: [`../api-contract.md`](../api-contract.md#save-vehicle) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Get" = `POST /Setting/GetVehicleById` `{"vehicleId": "<guid>"}`. "Save" = `POST /Setting/AddEditVechile` with the vehicle's ID, new values, and `lastUpdatedOn` from a fresh Get.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `VEH-03-D01` | Authentication | no token | Get `VEH-VAN-1` | 401 |
| `VEH-03-D02` | Company isolation | default | Get `VEH-B-VAN` (CO-B vehicle) | `status: false`, "Vehicle not found"; no CO-B data leaked |
| `VEH-03-D03` | Over-posting / isolation | default | Get `VEH-B-VAN` with `"companyId": <CO-B>` in body | `status: false`, "Vehicle not found" |
| `VEH-03-D04` | Rule bypass | default | Get deleted vehicle ID | "Vehicle not found" |
| `VEH-03-D05` | Validation / error hygiene | default | Get with random GUID, empty GUID, "abc", or missing body | "Vehicle not found" or 400 without stack trace; unexpected failure → "Something went wrong. Please try again." |
| `VEH-03-D06` | Validation | default | Save `VEH-VAN-1` with `regNo` "MH12CD5678" (belongs to `VEH-TRUCK-1`) | "Registration number already exists"; record unchanged |
| `VEH-03-D07` | Company isolation | default | Save with `VEH-B-VAN`'s ID and `regNo` "HIJACKED12" | "Vehicle not found"; CO-B row unchanged |
| `VEH-03-D08` | Rule bypass | default | Save `VEH-VAN-1` (active) with `"isActive": false` | `status: true`; details updated, still Active (`IsActive` changed only via status endpoint) |
| `VEH-03-D09` | Concurrency | A-ADMIN, A-ADMIN2 | Both Get `VEH-VAN-1`; user 1 saves; user 2 saves with old `lastUpdatedOn` | User 2 receives: "This vehicle was changed by someone else. Reload and try again."; user 1's data preserved |
| `VEH-03-D10` | Concurrency (bypass) | default | Save `VEH-VAN-1` without `lastUpdatedOn` or with null | "This vehicle was changed by someone else. Reload and try again."; record unchanged |
| `VEH-03-D11` | Permission | `TOKEN_A_VIEW` | Save `VEH-VAN-1` | "You don't have permission to perform this action."; unchanged |
| `VEH-03-D12` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Get; Save | 401 each |
