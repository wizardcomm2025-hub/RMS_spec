# Test cases — Department

Derived from the use cases, [`../data-model.md`](../data-model.md), [`../api-contract.md`](../api-contract.md) and [`../ui-ux.md`](../ui-ux.md). Rules: [`../../../conventions.md`](../../../conventions.md) → "Test cases". Results go by test ID in `RMS_API/specs/department/test-results.md`; testing runs manually at the end of the project.

Feature code: `DEP`

## Test data

Two companies on two hosts in the **same Tenant DB**.

| Fixture | State |
|---|---|
| CO-A / HOST-A, CO-B / HOST-B | As above |
| A-ADMIN, A-ADMIN2 | CO-A users with Department view, `create_department`, `edit_department` |
| A-VIEW | CO-A user who can open Department but has no create / edit |
| B-ADMIN, SA | CO-B admin; SuperAdmin |
| DEP-KITCHEN | CO-A "Kitchen", active, with active user U-COOK |
| DEP-SERVICE | CO-A "Service", active, with active roster pattern RP-LUNCH and no active users |
| DEP-BAR | CO-A "Bar", active, used only by inactive user U-OLDBAR and inactive roster pattern RP-OLD |
| DEP-STORE | CO-A "Store", active, unused |
| DEP-OLD | CO-A "Old Team", **inactive**, unused |
| DEP-DEL | CO-A "Closed Team", **deleted** (`IsDelete = 1`) |
| DEP-A-MORE | 6 more active CO-A departments, unused, so CO-A has **12** non-deleted departments (11 active + Old Team) |
| DEP-B-SALES | CO-B "Sales", active |

Tokens, headers and the empty GUID as in Setting → Location (`TOKEN_A_ADMIN`, `TOKEN_A_ADMIN2`, `TOKEN_A_VIEW`, `TOKEN_B_ADMIN`, `TOKEN_SA`, `TOKEN_EXPIRED`, `TOKEN_TAMPERED`; default `Origin: HOST-A`).

## Coverage

| Use case | Covers | Test IDs |
|---|---|---|
| 01 | AC 1 all 12, sorted, company only (D1) | DEP-01-T01, DEP-01-D04 |
| 01 | AC 2 inactive chip | DEP-01-T02 |
| 01 | AC 3 search + status | DEP-01-T03 |
| 01 | AC 4 no match | DEP-01-T04 |
| 01 | AC 5 empty | DEP-01-T05 |
| 01 | AC 6 load error | DEP-01-T06, DEP-01-D08 |
| 01 | AC 7 no pagination | DEP-01-T07, DEP-01-D07 |
| 01 | AC 8 Add hidden | DEP-01-T08 |
| 01 | AC 9 Roster gets all (D1) | DEP-01-T09 |
| 01 | AC 10 / UI AC 1–5 | DEP-01-T10 – T12 |
| 02 | AC 1 add | DEP-02-T01, DEP-02-D04 |
| 02 | AC 2 duplicate incl. inactive (D3) | DEP-02-T02, T03, DEP-02-D08, D09 |
| 02 | AC 3 other company | DEP-02-T04, DEP-02-D10 |
| 02 | AC 4 length | DEP-02-T05, DEP-02-D07 |
| 02 | AC 5 characters | DEP-02-T06, DEP-02-D06 |
| 02 | AC 6 duplicate never shows success (D2) | DEP-02-T02 |
| 02 | AC 7 over-posting | DEP-02-D05 |
| 02 | AC 8 permission | DEP-02-T07, DEP-02-D12 |
| 02 | Alt: discard, double click, failure | DEP-02-T08, T09, DEP-02-D11, D14 |
| 03 | AC 1 rename | DEP-03-T01 |
| 03 | AC 2 duplicate | DEP-03-T02, DEP-03-D06 |
| 03 | AC 3 inactive | DEP-03-T03 |
| 03 | AC 4 status not changed by save | DEP-03-D08 |
| 03 | AC 5 other company / deleted (D4) | DEP-03-D02 – D04, D07 |
| 03 | AC 6 stale | DEP-03-T04, DEP-03-D09, D10 |
| 03 | AC 7 permission | DEP-03-T05, DEP-03-D11 |
| 04 | AC 1 deactivate | DEP-04-T01, DEP-04-D04 |
| 04 | AC 2 hidden in User form | DEP-04-T02 |
| 04 | AC 3 active users block | DEP-04-T03, DEP-04-D05 |
| 04 | AC 4 roster pattern block | DEP-04-T04, DEP-04-D06 |
| 04 | AC 5 only inactive users / patterns | DEP-04-D07 |
| 04 | AC 6 cancel | DEP-04-T05 |
| 04 | AC 7 permission | DEP-04-T06, DEP-04-D10 |
| 04 | AC 8 other company | DEP-04-D03 |
| 04 | AC 9 delete blocked | DEP-04-D12, D13 |
| 04 | Alt: server failure | DEP-04-T07, DEP-04-D15 |

## Endpoint checklist (developer)

| Endpoint | Auth | Company isolation | SuperAdmin | Permission | Validation | Rule bypass | Over-posting | Concurrency | Injection | Error hygiene |
|---|---|---|---|---|---|---|---|---|---|---|
| `GetDepartmentList` | 01-D01, D02 | 01-D03, D04 | 01-D05 | n/a — read | 01-D06 | 01-D07 | 01-D06 | n/a — read | 01-D06 | 01-D08 |
| `GetDepartmentById` | 03-D01 | 03-D02, D03 | 03-D12 | n/a — read | 03-D05 | 03-D04 | 03-D03 | n/a | 03-D05 | 03-D05 |
| `AddEditDepartment` | 02-D01, D02 | 02-D03, 03-D07 | 02-D13 | 02-D12, 03-D11 | 02-D06–D09 | 03-D08 | 02-D05 | 02-D11, 03-D09, D10 | 02-D06 | 02-D14 |
| `ValidateDeptName` | 02-D15 | 02-D16 | 02-D13 | n/a — read | 02-D17 | n/a | n/a | n/a | 02-D17 | 02-D17 |
| `ChangeDepartmentStatus` | 04-D01 | 04-D02, D03 | 04-D11 | 04-D10 | 04-D08 | 04-D05–D07 | 04-D09 | 04-D14 | n/a — guid / bool | 04-D15 |
| `DeleteDepartment` (blocked) | n/a — refused for everyone | 04-D13 | 04-D11 | n/a | n/a | 04-D12 | n/a | n/a | n/a | n/a |

## Files

| Use case | Tester | Developer |
|---|---|---|
| 01 | [`01-view-and-search-departments.tester.md`](./01-view-and-search-departments.tester.md) | [`01-view-and-search-departments.developer.md`](./01-view-and-search-departments.developer.md) |
| 02 | [`02-add-department.tester.md`](./02-add-department.tester.md) | [`02-add-department.developer.md`](./02-add-department.developer.md) |
| 03 | [`03-rename-department.tester.md`](./03-rename-department.tester.md) | [`03-rename-department.developer.md`](./03-rename-department.developer.md) |
| 04 | [`04-switch-department-on-off.tester.md`](./04-switch-department-on-off.tester.md) | [`04-switch-department-on-off.developer.md`](./04-switch-department-on-off.developer.md) |
