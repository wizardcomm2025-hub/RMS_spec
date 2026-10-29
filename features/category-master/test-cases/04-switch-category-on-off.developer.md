# 04 — Switch a category on or off — developer test cases

- Use case: [`../04-switch-category-on-off.md`](../04-switch-category-on-off.md) · API: [`../api-contract.md`](../api-contract.md#change-status) · Fixtures: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Status" = `POST /Setting/ChangeStockGroupStatus` (new).

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `CAT-04-D01` | Authentication | no token | Status `{ "stockGroupId": <CAT-DAIRY>, "isActive": false }` | 401; unchanged |
| `CAT-04-D02` | Authentication | `TOKEN_EXPIRED`; `TOKEN_A_ADMIN` · HOST-B | Same | 401 / 401 "Token does not match this site's company."; unchanged |
| `CAT-04-D03` | Company isolation | default | Deactivate CAT-B-SPICE | "Category not found"; CAT-B-SPICE still active |
| `CAT-04-D04` | Company isolation | default | Deactivate ASSET-A; activate CAT-DEL | "Category not found" each; unchanged |
| `CAT-04-D05` | Main flow | default | Deactivate CAT-DAIRY (only an inactive item uses it) | `status: true`; `IsActive = 0`, `IsDeleted = 0`, `UpdatedBy` = A-ADMIN, `UpdatedAt` changed. Reactivate → `IsActive = 1` |
| `CAT-04-D06` | Rule bypass | default | Deactivate CAT-VEG (active subs) | "Deactivate its sub-categories first."; unchanged |
| `CAT-04-D07` | Rule bypass | default | Deactivate CAT-LEAFY (active item as `SubStockGroupId`) | "This category is used by active items. Move or deactivate them first." |
| `CAT-04-D08` | Rule bypass | default | Setup (SSMS): set ITEM-SPINACH's `SubStockGroupId` to NULL (it keeps `StockGroupId` = CAT-VEG). Deactivate CAT-LEAFY and CAT-ROOT (both succeed). Then deactivate CAT-VEG. Restore ITEM-SPINACH afterwards | CAT-VEG: "This category is used by active items. Move or deactivate them first."; CAT-VEG stays active |
| `CAT-04-D09` | Rule bypass | default | Activate CAT-OLDSUB while CAT-OLD is inactive | "Activate the parent category first."; unchanged |
| `CAT-04-D10` | Validation | default | `isActive` missing / null / "maybe"; `stockGroupId` missing / "abc" / empty GUID | 400 or `status: false` ("Category not found" for GUIDs) without stack trace; nothing changes |
| `CAT-04-D11` | Over-posting | default | `{ stockGroupId: CAT-DAIRY, isActive: false, "CompanyId": <CO-B>, "IsDeleted": true }` | Deactivated in CO-A; `IsDeleted` stays 0; `UpdatedBy` = A-ADMIN |
| `CAT-04-D12` | Permission | `TOKEN_A_VIEW` | Deactivate CAT-DAIRY | "You don't have permission to perform this action."; unchanged |
| `CAT-04-D13` | SuperAdmin boundary | `TOKEN_SA` · HOST-A | Status; `DelStockGroupById` | 401 each |
| `CAT-04-D14` | Rule bypass (delete) | default | `POST /Setting/DelStockGroupById` `{ "stockGroupId": <CAT-DAIRY> }` | "Categories cannot be deleted. Deactivate the category instead."; unchanged (today it is deleted) |
| `CAT-04-D15` | Company isolation | default | `DelStockGroupById` with CAT-B-SPICE | Same refusal; CAT-B-SPICE unchanged (today another company's category is deleted — D3) |
| `CAT-04-D16` | Concurrency | default | Deactivate and activate CAT-DAIRY in parallel, 10 rounds; and deactivate CAT-VEG while adding a sub to it in parallel | Always one valid state; never an inactive main with an active sub |
| `CAT-04-D17` | Error hygiene | default | Status while `SP_ChangeStockGroupStatus` fails | "Something went wrong. Please try again."; no SQL or stack trace |
