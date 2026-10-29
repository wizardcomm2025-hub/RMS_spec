# 04 — Switch a menu section on or off — developer test cases

- Use case: [`../04-switch-menu-section-on-off.md`](../04-switch-menu-section-on-off.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Status" = `POST /Setting/ChangeMenuSectionStatus` (new).

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `MSE-04-D01` | Authentication | no token; `TOKEN_EXPIRED` | Status `{ "menuSectionId": <MSE-DRINKS>, "isActive": false }` | 401; unchanged |
| `MSE-04-D02` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Same | 401 |
| `MSE-04-D03` | Company isolation (D2) | default | Deactivate MSE-B-MAIN; id 999999 | "Menu section not found"; unchanged |
| `MSE-04-D04` | Main flow | default | Deactivate MSE-DESSERTS; reactivate | `status: true`; `IsActive` 0 then 1; `UpdatedBy` = A-ADMIN; `UpdatedAt` changes |
| `MSE-04-D05` | Rule bypass (Q1) | default | Deactivate MSE-STARTERS | "This menu section is used by active recipes. Move them to another section first."; unchanged |
| `MSE-04-D06` | Business rule | default | Deactivate MSE-DRINKS (unused) | `status: true` |
| `MSE-04-D07` | Validation | default | `isActive` missing / "maybe"; `menuSectionId` "abc" / 0 | 400 or `status: false`; no stack trace |
| `MSE-04-D08` | Over-posting | default | `{ menuSectionId: MSE-DRINKS, isActive: false, "menuSectionName": "X", "companyId": <CO-B> }` | Deactivated; name unchanged |
| `MSE-04-D09` | Permission | `TOKEN_A_VIEW` | Deactivate MSE-DRINKS | "You don't have permission to perform this action." |
| `MSE-04-D10` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Status; `DeleteMenuSection` | 401 each |
| `MSE-04-D11` | Rule bypass (delete, D2) | default | `POST /Setting/DeleteMenuSection` with MSE-DRINKS; with MSE-B-MAIN | "Menu sections cannot be deleted. Deactivate the menu section instead."; both unchanged (today CO-B's is switched off) |
| `MSE-04-D12` | Concurrency | default | Deactivate / activate MSE-DRINKS in parallel, 10 rounds | Each round ends in one valid state |
| `MSE-04-D13` | Error hygiene | default | Status while the procedure fails | "Something went wrong. Please try again."; no stack trace |
