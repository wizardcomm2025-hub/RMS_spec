# 04 — Switch a reason on or off — developer test cases

- Use case: [`../04-switch-reason-on-off.md`](../04-switch-reason-on-off.md) · API: [`../api-contract.md`](../api-contract.md#change-status) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Status" = `POST /Setting/WestageReasonActive`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `RSN-04-D01` | Authentication | no token; `TOKEN_EXPIRED` | Status `{ "reasonMasterId": <RSN-DAMAGED>, "isActive": false }` | 401; unchanged |
| `RSN-04-D02` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Same | 401 "Token does not match this site's company." |
| `RSN-04-D03` | Company isolation (D6) | default | Deactivate RSN-B-LEAK; activate RSN-DEL; id 999999 | "Reason not found" each; unchanged (today `OK`, and the deleted row could be re-activated) |
| `RSN-04-D04` | Main flow | default | Deactivate RSN-EXPIRED; reactivate | `status: true`; `IsActive` 0 then 1; `IsDelete = 0`; `UpdatedBy` = A-ADMIN; `UpdatedOn` changes; W-1 untouched |
| `RSN-04-D05` | Rule bypass | default | Deactivate RSN-EXPIRED; then `AddEditWastage` from Wastage Entry's normal flow | List `Active` no longer returns it (the Wastage save API itself is out of scope) |
| `RSN-04-D06` | Business rule | default | Deactivate every active reason | All succeed (no "last reason" rule); `openMode: "Active"` returns 0 rows |
| `RSN-04-D07` | Validation | default | `isActive` missing / null / "maybe"; `reasonMasterId` missing / "abc" / 0 | 400 or `status: false` ("Reason not found" for numbers); no stack trace; nothing changes |
| `RSN-04-D08` | Over-posting | default | `{ reasonMasterId: RSN-DAMAGED, isActive: false, "companyId": <CO-B>, "isDelete": true, "description": "x" }` | Deactivated in CO-A; `IsDelete` and text unchanged |
| `RSN-04-D09` | Permission | `TOKEN_A_VIEW` | Deactivate RSN-DAMAGED | "You don't have permission to perform this action."; unchanged |
| `RSN-04-D10` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Status; `DeleteWastageById` | 401 each |
| `RSN-04-D11` | Rule bypass (delete) | default | `POST /Setting/DeleteWastageById` `{ "reasonMasterId": <RSN-DAMAGED> }` | "Reasons cannot be deleted. Deactivate the reason instead."; `IsDelete` stays 0 (today it is set to 1) |
| `RSN-04-D12` | Company isolation | default | `DeleteWastageById` with RSN-B-LEAK | Same refusal; unchanged |
| `RSN-04-D13` | Concurrency | default | Deactivate and activate RSN-DAMAGED in parallel, 10 rounds | Each round ends in one valid state; no error leaks |
| `RSN-04-D14` | Error hygiene | default | Status while `SP_WestageReasonActive` fails | "Something went wrong. Please try again."; no SQL or stack trace |
