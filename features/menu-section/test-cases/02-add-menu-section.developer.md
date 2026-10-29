# 02 — Add a menu section — developer test cases

- Use case: [`../02-add-menu-section.md`](../02-add-menu-section.md) · API: [`../api-contract.md`](../api-contract.md) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Save" = `POST /Setting/AddEditMenuSection` with `menuSectionId: 0`; "Validate" = `POST /Users/ValidateMenuName`.

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `MSE-02-D01` | Authentication | no token; `TOKEN_EXPIRED` | Save `{ "menuSectionId": 0, "menuSectionName": "Combos" }` | 401; nothing inserted |
| `MSE-02-D02` | Authentication | `TOKEN_TAMPERED` | Same | 401 |
| `MSE-02-D03` | Company isolation | `TOKEN_A_ADMIN` · HOST-B | Same | 401 "Token does not match this site's company." |
| `MSE-02-D04` | Over-posting | default | `{ "menuSectionId": 0, "menuSectionName": "Combos 2", "isActive": false, "companyId": <CO-B>, "meta_MenuSectionId": 3 }` | Saved in CO-A, `IsActive = 1`, `Meta_MenuSectionId` NULL, `CreatedBy` = A-ADMIN, `CreatedAt` ≈ UTC now |
| `MSE-02-D05` | Validation / injection | default | name missing, `"   "`, `"Soup@Night"`, `"x'; DROP TABLE MenuSection;--"`, 101 characters | Required / characters / length messages; nothing inserted; table intact |
| `MSE-02-D06` | Business rule (D3, D4) | default | " STARTERS "; "old specials" | "Menu section name already exists", `status: false` each (today `status: true`) |
| `MSE-02-D07` | Company isolation | default | "Main Course" (CO-B only) | `status: true` |
| `MSE-02-D08` | Validate | default | Validate " starters " / "old specials" / "Starters" with Starters' own id / `"' OR 1=1 --"` | `true` / `true` / `false` / `false`; no error |
| `MSE-02-D09` | Validate isolation | default | Validate "Main Course" | `false` |
| `MSE-02-D10` | Concurrency | `TOKEN_A_ADMIN` and `TOKEN_A_ADMIN2` | 10 parallel saves of "Race Section" | One row; the rest the duplicate message |
| `MSE-02-D11` | Permission | `TOKEN_A_VIEW` | Save | "You don't have permission to perform this action."; nothing inserted |
| `MSE-02-D12` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Save; Validate | 401 each |
| `MSE-02-D13` | Error hygiene | default | Save while the procedure fails | "Something went wrong. Please try again."; no SQL or stack trace |
| `MSE-02-D14` | Authentication | no token | Validate | 401 |
