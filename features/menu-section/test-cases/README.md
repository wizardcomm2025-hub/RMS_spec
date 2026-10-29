# Test cases — Menu Section

Derived from the use cases, [`../data-model.md`](../data-model.md), [`../api-contract.md`](../api-contract.md) and [`../ui-ux.md`](../ui-ux.md). Results by test ID in `RMS_API/specs/menu-section/test-results.md`; testing is manual at the end of the project.

Feature code: `MSE`

## Test data

Two companies on two hosts in the same Tenant DB.

| Fixture | State |
|---|---|
| CO-A / HOST-A, CO-B / HOST-B | As in Setting → Location |
| A-ADMIN, A-ADMIN2 | CO-A users with Menu Section view, `create_menu_section`, `edit_menu_section`, and Recipe access |
| A-VIEW | CO-A user who can open Menu Section but has no create / edit |
| B-ADMIN, SA | CO-B admin; SuperAdmin |
| MSE-STARTERS | CO-A "Starters", active, used by active recipe R-SOUP |
| MSE-DESSERTS | CO-A "Desserts", active, used only by an inactive recipe R-OLDCAKE and a deleted recipe |
| MSE-DRINKS | CO-A "Beverages", active, unused |
| MSE-OLD | CO-A "Old Specials", **inactive**, used by inactive recipe R-OLDSPECIAL (to test D7, make R-OLDSPECIAL editable) |
| MSE-A-MORE | 8 more active, unused CO-A sections → CO-A has **12** (11 active + Old Specials) |
| MSE-B-MAIN | CO-B "Main Course", active |

Tokens and headers as in Setting → Location (`TOKEN_A_ADMIN`, `TOKEN_A_ADMIN2`, `TOKEN_A_VIEW`, `TOKEN_B_ADMIN`, `TOKEN_SA`, `TOKEN_EXPIRED`, `TOKEN_TAMPERED`; default `Origin: HOST-A`).

## Coverage

| Use case | Test IDs |
|---|---|
| 01 | MSE-01-T01 – T08, MSE-01-D01 – D07 |
| 02 | MSE-02-T01 – T07, MSE-02-D01 – D14 |
| 03 | MSE-03-T01 – T05, MSE-03-D01 – D11 |
| 04 | MSE-04-T01 – T08, MSE-04-D01 – D13 |

## Endpoint checklist (developer)

| Endpoint | Auth | Company isolation | SuperAdmin | Permission | Validation | Rule bypass | Over-posting | Concurrency | Injection | Error hygiene |
|---|---|---|---|---|---|---|---|---|---|---|
| `GetMenuSectionWithPagination` | 01-D01, D02 | 01-D03, D04 | 01-D05 | n/a | 01-D06 | n/a | 01-D06 | n/a | 01-D06 | 01-D07 |
| `GetMenuSectionById` | 03-D01 | 03-D02, D03 | 03-D10 | n/a | 03-D04 | n/a | n/a | n/a | 03-D04 | 03-D04 |
| `AddEditMenuSection` | 02-D01, D02 | 02-D03, 03-D05 | 02-D12 | 02-D11, 03-D09 | 02-D05, D06 | 03-D06 | 02-D04 | 02-D10, 03-D07, D08 | 02-D05 | 02-D13 |
| `ValidateMenuName` | 02-D14 | 02-D09 | 02-D12 | n/a | 02-D08 | n/a | n/a | n/a | 02-D08 | 02-D08 |
| `ChangeMenuSectionStatus` | 04-D01 | 04-D02, D03 | 04-D10 | 04-D09 | 04-D07 | 04-D05 | 04-D08 | 04-D12 | n/a | 04-D13 |
| `DeleteMenuSection` (blocked) | n/a | 04-D11 | 04-D10 | n/a | n/a | 04-D11 | n/a | n/a | n/a | n/a |

## Files

| Use case | Tester | Developer |
|---|---|---|
| 01 | [`01-view-and-search-menu-sections.tester.md`](./01-view-and-search-menu-sections.tester.md) | [`01-view-and-search-menu-sections.developer.md`](./01-view-and-search-menu-sections.developer.md) |
| 02 | [`02-add-menu-section.tester.md`](./02-add-menu-section.tester.md) | [`02-add-menu-section.developer.md`](./02-add-menu-section.developer.md) |
| 03 | [`03-rename-menu-section.tester.md`](./03-rename-menu-section.tester.md) | [`03-rename-menu-section.developer.md`](./03-rename-menu-section.developer.md) |
| 04 | [`04-switch-menu-section-on-off.tester.md`](./04-switch-menu-section-on-off.tester.md) | [`04-switch-menu-section-on-off.developer.md`](./04-switch-menu-section-on-off.developer.md) |
