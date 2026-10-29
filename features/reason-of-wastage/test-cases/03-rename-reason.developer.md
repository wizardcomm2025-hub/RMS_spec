# 03 — Rename a reason — developer test cases

- Use case: [`../03-rename-reason.md`](../03-rename-reason.md) · API: [`../api-contract.md`](../api-contract.md#save-reason) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Get" = `POST /Setting/GetWestageReasonById`; "Save" = `POST /Setting/AddEditResonMaster` with `reasonMasterId > 0` and the `updatedOn` from Get.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `RSN-03-D01` | Authentication | no token; `TOKEN_EXPIRED` | Get RSN-DAMAGED; Save it | 401 each |
| `RSN-03-D02` | Company isolation | default | Get RSN-B-LEAK | "Reason not found", `status: false` |
| `RSN-03-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Get RSN-DAMAGED | 401 "Token does not match this site's company." |
| `RSN-03-D04` | Rule bypass (D5) | default | Get RSN-DEL; Get 999999 | "Reason not found" each (today RSN-DEL is returned) |
| `RSN-03-D05` | Validation / error hygiene | default | Get `{ "reasonMasterId": "abc" }`, `-1`, `{}` | 400 or "Reason not found"; no stack trace |
| `RSN-03-D06` | Business rule | default | Save RSN-DAMAGED as " SPOILED " | "Reason already exists"; unchanged |
| `RSN-03-D07` | Company isolation (D5) | default | Save RSN-B-LEAK (any `updatedOn`); Save RSN-DEL | "Reason not found" each; nothing changes (today `OK`) |
| `RSN-03-D08` | Rule bypass | default | Save RSN-DAMAGED `{ "description": "Damaged 2", "isActive": false }` | `status: true`; text changed; `IsActive` stays 1 |
| `RSN-03-D09` | Concurrency (stale) | default | Get RSN-SPOILED; A-ADMIN2 saves it; A-ADMIN saves with the old `updatedOn` | "This reason was changed by someone else. Reload and try again."; A-ADMIN2's text kept |
| `RSN-03-D10` | Concurrency (missing token) | default | Save RSN-SPOILED without `updatedOn`; with `"updatedOn": "2020-01-01"` | Stale message each; unchanged |
| `RSN-03-D11` | Permission | `TOKEN_A_VIEW` | Save RSN-DAMAGED with a valid token | "You don't have permission to perform this action."; unchanged |
| `RSN-03-D12` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Get; Save | 401 each |
| `RSN-03-D13` | Edit token (NULL `UpdatedOn`) | default | Get RSN-NULLDATE; Save it with the returned `updatedOn` | Get returns a non-null `updatedOn`; Save `status: true`; `UpdatedOn` now set |
