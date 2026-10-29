# 03 — Edit a driver — developer test cases

- Use case: [`../03-edit-driver.md`](../03-edit-driver.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Get" = `POST /Setting/GetDriverById`; "Save" = `POST /Setting/AddEditDriver` with an existing `driverId` and the `lastUpdatedOn` from Get.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `DRV-03-D01` | Authentication | no token | Get DRV_1; Save DRV_1 | 401 each |
| `DRV-03-D02` | Main flow | default | Get DRV_1; Save "Rajesh K. Kumar", same licence, `profileImage: null` | `status: true`; name changed; photo unchanged; `LastUpdatedBy` = A-ADMIN, `LastUpdatedOn` changes; `IsActive` unchanged |
| `DRV-03-D03` | Company isolation | default | Get DRV_B | "Driver not found", `status: false` |
| `DRV-03-D04` | Company isolation (D3) | default | Save DRV_B's id with name "Hijacked" (any `lastUpdatedOn`) | "Driver not found"; DRV_B unchanged in CO-B (today it is overwritten) |
| `DRV-03-D05` | Validation | default | Get `{ "driverId": "abc" }`, `{}`, unknown GUID | 400 or "Driver not found"; no stack trace |
| `DRV-03-D06` | Business rule | default | Save DRV_1 with DRV_2's licence | "License number already exists"; unchanged |
| `DRV-03-D07` | Business rule | default | Validate DRV_1's own licence with DRV_1's id | `rtrValue: false` |
| `DRV-03-D08` | Rule bypass | default | Save DRV_1 with `"isActive": 0` | Saved; `IsActive` stays 1 |
| `DRV-03-D09` | Concurrency (stale) | default | Get DRV_1; A-ADMIN2 saves it; A-ADMIN saves with the old `lastUpdatedOn` | "This driver was changed by someone else. Reload and try again."; A-ADMIN2's change kept |
| `DRV-03-D10` | Concurrency (missing token) | default | Save DRV_1 without `lastUpdatedOn` | Stale message; unchanged |
| `DRV-03-D11` | Photo | default | Save DRV_1 with `profileImage: ""` | `status: true`; `ProfileImage` cleared |
| `DRV-03-D12` | Permission | `TOKEN_A_VIEW` | Save DRV_1 | "You don't have permission to perform this action."; unchanged |
| `DRV-03-D13` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Get; Save | 401 each |
