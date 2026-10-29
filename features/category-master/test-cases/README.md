# Test cases — Category Master

Derived from the use cases, [`../data-model.md`](../data-model.md), [`../api-contract.md`](../api-contract.md) and [`../ui-ux.md`](../ui-ux.md). Rules: [`../../../conventions.md`](../../../conventions.md) → "Test cases".

- **Tester** (`NN-*.tester.md`): through the admin dashboard; exact on-screen text; no endpoints.
- **Developer** (`NN-*.developer.md`): direct API calls (Postman or any HTTP client) with the expected HTTP status, `status` / exact `message`, and the database state after the call.

Results are recorded by test ID in `RMS_API/specs/category-master/test-results.md`. Testing is run manually at the end of the project (decision 2026-10-28).

Feature code: `CAT`

## Test data

Two companies on two hosts **in the same Tenant DB**. Each has inventory types **Raw Material** (RM) and **Finished Goods** (FG); CO-B also has **Packaging** (PK), which CO-A does not.

| Fixture | State |
|---|---|
| CO-A / HOST-A, CO-B / HOST-B | As above |
| A-ADMIN | CO-A user with Category Master view, `create_item_category`, `edit_item_category` |
| A-ADMIN2 | Second CO-A user with the same role |
| A-VIEW | CO-A user who can open Category Master but has no create / edit |
| B-ADMIN | CO-B admin |
| SA | SuperAdmin |
| CAT-VEG | CO-A, RM, main, "Vegetables", code VEG, active |
| CAT-LEAFY | CO-A, RM, sub of CAT-VEG, "Leafy Greens", code LEAFY, active, used by active item ITEM-SPINACH (as `SubStockGroupId`) |
| CAT-ROOT | CO-A, RM, sub of CAT-VEG, "Root Vegetables", code ROOT, active, no items |
| CAT-DAIRY | CO-A, RM, main, "Dairy", code DAIRY, active, no subs, no items, has an `ImagePath` and `IsIntangible = 1` |
| CAT-OLD | CO-A, RM, main, "Old Stock", code OLD, **inactive**, with inactive sub CAT-OLDSUB "Old Sub" (code OLDSUB) |
| CAT-DEL | CO-A, RM, "Closed Group", **deleted** |
| CAT-FGVEG | CO-A, **FG**, main, "Vegetables", code VEG (same name and code as CAT-VEG, other type), active |
| CAT-B-VEG | CO-B, RM, main, "Vegetables", code VEG, active |
| CAT-B-SPICE | CO-B, RM, main, "Spices", code SPICE, active (name not used in CO-A) |
| ASSET-A | CO-A asset category (`ClassificationId = 2`), "Laptops" |
| ITEM-SPINACH | CO-A active stock item in CAT-VEG / CAT-LEAFY |
| ITEM-OLDMILK | CO-A **inactive** stock item in CAT-DAIRY |

## Tokens and headers (developer)

As in Setting → Location: `TOKEN_A_ADMIN`, `TOKEN_A_ADMIN2`, `TOKEN_A_VIEW`, `TOKEN_B_ADMIN`, `TOKEN_SA`, `TOKEN_EXPIRED`, `TOKEN_TAMPERED`. Default headers: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. Empty GUID = `00000000-0000-0000-0000-000000000000`.

## Coverage

| Use case | Covers | Test IDs |
|---|---|---|
| 01 | AC 1 two levels, company only, no assets | CAT-01-T01, CAT-01-D04, D08 |
| 01 | AC 2 inactive shown | CAT-01-T02, CAT-01-D07 |
| 01 | AC 3 search + filters | CAT-01-T03, T04 |
| 01 | AC 4 no match | CAT-01-T05 |
| 01 | AC 5 empty | CAT-01-T06 |
| 01 | AC 6 load error | CAT-01-T07, CAT-01-D10 |
| 01 | AC 7 expand / "N sub" | CAT-01-T08 |
| 01 | AC 8 no pagination (one page) | CAT-01-T16 (CAT-01-T09 removed) |
| 01 | AC 9 Add hidden | CAT-01-T10 |
| 01 | AC 10 / UI AC 1–6 | CAT-01-T11 – T15 |
| 01 | AC 11 search on a main category shows its subs | CAT-01-T17 |
| 02 | AC 1 add main | CAT-02-T01, CAT-02-D04 |
| 02 | AC 2 add sub | CAT-02-T02 |
| 02 | AC 3 duplicate name | CAT-02-T03, T04, CAT-02-D11, D12 |
| 02 | AC 4 same name other type | CAT-02-T05, CAT-02-D13 |
| 02 | AC 5 code rules | CAT-02-T06, T07, CAT-02-D14 – D16 |
| 02 | AC 6 length | CAT-02-T08, CAT-02-D09 |
| 02 | AC 7 invalid parent | CAT-02-D18 – D21 |
| 02 | AC 8 over-posting | CAT-02-D05, D06 |
| 02 | AC 9 permission | CAT-02-T10, CAT-02-D22 |
| 02 | AC 10 code suggestion | CAT-02-T09 |
| 02 | UI searchable parent dropdown | CAT-02-T15 |
| 02 | Alt: characters, type, discard, double click, failure | CAT-02-T11 – T14, CAT-02-D08, D10, D17, D23, D24 |
| 03 | AC 1 edit | CAT-03-T01 |
| 03 | AC 2 duplicate on edit | CAT-03-T02, CAT-03-D08 |
| 03 | AC 3 main with subs → sub | CAT-03-T03, CAT-03-D10 |
| 03 | AC 4 type locked | CAT-03-T04, CAT-03-D11 |
| 03 | AC 5 hidden fields kept | CAT-03-D13 |
| 03 | AC 6 other company / asset | CAT-03-D02 – D05, D09 |
| 03 | AC 7 stale | CAT-03-T05, CAT-03-D14, D15 |
| 03 | AC 8 permission | CAT-03-T06, CAT-03-D16 |
| 03 | AC 9 inactive edit | CAT-03-T07 |
| 03 | Alt: same-name rename, parent = itself, not found | CAT-03-T08, T09, CAT-03-D06, D12 |
| 04 | AC 1 deactivate | CAT-04-T01, CAT-04-D05 |
| 04 | AC 2 hidden elsewhere | CAT-04-T02 |
| 04 | AC 3 active subs block | CAT-04-T03, CAT-04-D06 |
| 04 | AC 4 active items block | CAT-04-T04, CAT-04-D07, D08 |
| 04 | AC 5 parent inactive | CAT-04-T05, CAT-04-D09 |
| 04 | AC 6 cancel | CAT-04-T06 |
| 04 | AC 7 permission | CAT-04-T07, CAT-04-D12 |
| 04 | AC 8 other company / asset | CAT-04-D03, D04 |
| 04 | AC 9 delete blocked | CAT-04-D14, D15 |

## Endpoint checklist (developer)

| Endpoint | Auth | Company isolation | SuperAdmin | Permission | Validation | Rule bypass | Over-posting | Concurrency | Injection | Error hygiene |
|---|---|---|---|---|---|---|---|---|---|---|
| `GetStockGroupList` | 01-D01–D03 | 01-D04–D06, D08 | 01-D09 | n/a — read | n/a — no body | 01-D07 | n/a — no body | n/a — read | n/a | 01-D10 |
| `GetInventoryTypeCompanyWise` | 01-D01 | 01-D11 | 01-D09 | n/a — read | n/a | n/a | n/a | n/a | n/a | 01-D10 |
| `GetStockGroupById` | 03-D01 | 03-D02, D03, D05 | 03-D17 | n/a — read | 03-D06, D07 | 03-D04 | 03-D05 | n/a | 03-D07 | 03-D07 |
| `AddEditStockGroup` | 02-D01–D03 | 02-D07, 03-D09 | 02-D25 | 02-D22, 03-D16 | 02-D08–D21 | 03-D10–D13 | 02-D05, D06 | 02-D23, 03-D14, D15 | 02-D10, D24 | 02-D26 |
| `ValidateStockGroupName` | 02-D27 | 02-D28 | 02-D25 | n/a — read | 02-D29 | n/a | n/a | n/a | 02-D29 | 02-D29 |
| `ChangeStockGroupStatus` | 04-D01, D02 | 04-D03, D04 | 04-D13 | 04-D12 | 04-D10 | 04-D06–D09 | 04-D11 | 04-D16 | n/a — guid / bool | 04-D17 |
| `GetStockGroupByInventoryType` | 02-D30 | 02-D30 | 02-D25 | n/a — read | n/a | n/a | n/a | n/a | n/a | n/a |
| `DelStockGroupById` (blocked) | 04-D14 | 04-D15 | 04-D13 | n/a — always refused | n/a | 04-D14 | n/a | n/a | n/a | n/a |

## Files

| Use case | Tester | Developer |
|---|---|---|
| 01 | [`01-view-and-filter-categories.tester.md`](./01-view-and-filter-categories.tester.md) | [`01-view-and-filter-categories.developer.md`](./01-view-and-filter-categories.developer.md) |
| 02 | [`02-add-category.tester.md`](./02-add-category.tester.md) | [`02-add-category.developer.md`](./02-add-category.developer.md) |
| 03 | [`03-edit-category.tester.md`](./03-edit-category.tester.md) | [`03-edit-category.developer.md`](./03-edit-category.developer.md) |
| 04 | [`04-switch-category-on-off.tester.md`](./04-switch-category-on-off.tester.md) | [`04-switch-category-on-off.developer.md`](./04-switch-category-on-off.developer.md) |
