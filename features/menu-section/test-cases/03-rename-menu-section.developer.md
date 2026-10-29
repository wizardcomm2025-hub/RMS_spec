# 03 — Rename a menu section — developer test cases

- Use case: [`../03-rename-menu-section.md`](../03-rename-menu-section.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Get" = `POST /Setting/GetMenuSectionById`; "Save" = `POST /Setting/AddEditMenuSection` with an id and the `updatedAt` from Get.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `MSE-03-D01` | Authentication | no token | Get; Save | 401 each |
| `MSE-03-D02` | Company isolation (D2) | default | Get MSE-B-MAIN | "Menu section not found" (today CO-B's row is returned) |
| `MSE-03-D03` | Business rule (D5) | default | Get MSE-OLD (inactive) | `status: true`, its own data and `updatedAt` |
| `MSE-03-D04` | Validation | default | Get `{ "menuSectionId": "abc" }`, `-1`, `{}` | 400 or "Menu section not found"; no stack trace |
| `MSE-03-D05` | Company isolation (D1) | default | Save MSE-B-MAIN's id with name "Hijacked" | "Menu section not found"; CO-B's row unchanged (today renamed) |
| `MSE-03-D06` | Rule bypass | default | Save MSE-DRINKS with `"isActive": false` | Renamed; `IsActive` stays 1 |
| `MSE-03-D07` | Concurrency (stale) | default | Get; A-ADMIN2 saves; A-ADMIN saves with the old token | Stale message; A-ADMIN2's name kept |
| `MSE-03-D08` | Concurrency (missing token) | default | Save without `updatedAt` | Stale message |
| `MSE-03-D09` | Permission | `TOKEN_A_VIEW` | Save with a valid token | "You don't have permission to perform this action." |
| `MSE-03-D10` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Get; Save | 401 each |
| `MSE-03-D11` | Business rule | default | Save MSE-DRINKS with a duplicate name | "Menu section name already exists" |
