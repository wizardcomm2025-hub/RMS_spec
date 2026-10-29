# Test cases — Reason of Wastage

Derived from the use cases, [`../data-model.md`](../data-model.md), [`../api-contract.md`](../api-contract.md) and [`../ui-ux.md`](../ui-ux.md). Rules: [`../../../conventions.md`](../../../conventions.md) → "Test cases". Results go by test ID in `RMS_API/specs/reason-of-wastage/test-results.md`; testing runs manually at the end of the project.

Feature code: `RSN`

## Test data

Two companies on two hosts in the **same Tenant DB**.

| Fixture | State |
|---|---|
| CO-A / HOST-A, CO-B / HOST-B | As above |
| A-ADMIN, A-ADMIN2 | CO-A users with Reason of Wastage view, `create_reason_of_wastage`, `edit_reason_of_wastage`, and Wastage save access |
| A-VIEW | CO-A user who can open Reason of Wastage and Wastage (save) but has no reason create / edit |
| B-ADMIN, SA | CO-B admin; SuperAdmin |
| RSN-EXPIRED | CO-A "Expired", active, used by past wastage line W-1 |
| RSN-SPOILED | CO-A "Spoiled", active |
| RSN-DAMAGED | CO-A "Damaged", active, unused |
| RSN-OLD | CO-A "Old Reason", **inactive** |
| RSN-DEL | CO-A "Theft", **deleted** (`IsDelete = 1`) |
| RSN-A-MORE | 7 more active CO-A reasons, so CO-A has **11** non-deleted reasons (10 active + Old Reason) |
| RSN-B-LEAK | CO-B "Leak", active |
| RSN-NULLDATE | CO-A "Burnt", active, `UpdatedOn = NULL` (set by SQL for the token test) |

Tokens, headers as in Setting → Location (`TOKEN_A_ADMIN`, `TOKEN_A_ADMIN2`, `TOKEN_A_VIEW`, `TOKEN_B_ADMIN`, `TOKEN_SA`, `TOKEN_EXPIRED`, `TOKEN_TAMPERED`; default `Origin: HOST-A`). With RSN-NULLDATE the total is **12** non-deleted reasons (11 active + Old Reason).

## Coverage

| Use case | Covers | Test IDs |
|---|---|---|
| 01 | AC 1 all 12, sorted, company only | RSN-01-T01, RSN-01-D04 |
| 01 | AC 2 inactive chip | RSN-01-T02 |
| 01 | AC 3 search + status | RSN-01-T03 |
| 01 | AC 4 no match · AC 5 empty | RSN-01-T04, T05 |
| 01 | AC 6 load error | RSN-01-T06, RSN-01-D08 |
| 01 | AC 7 no pagination | RSN-01-T07, RSN-01-D07 |
| 01 | AC 8 Add hidden | RSN-01-T08 |
| 01 | AC 9 Wastage Entry active only, search independent (D1, D2) | RSN-01-T09, RSN-01-D05 |
| 01 | AC 10 / UI AC 1–5 | RSN-01-T10, T11 |
| 02 | AC 1 add | RSN-02-T01, RSN-02-D04 |
| 02 | AC 2 duplicate incl. inactive | RSN-02-T02, RSN-02-D08 |
| 02 | AC 3 deleted doesn't block (D4) | RSN-02-D09 |
| 02 | AC 4 other company | RSN-02-D10 |
| 02 | AC 5 characters | RSN-02-T03, RSN-02-D06 |
| 02 | AC 6 long text | RSN-02-T04, RSN-02-D07 |
| 02 | AC 7, 10 quick-add (D3) | RSN-02-T05, T06 |
| 02 | AC 8 over-posting | RSN-02-D05 |
| 02 | AC 9 permission | RSN-02-T07, RSN-02-D12 |
| 02 | Alt: discard, double click, failure | RSN-02-T08, RSN-02-D11, D14 |
| 03 | AC 1 rename shows everywhere | RSN-03-T01 |
| 03 | AC 2 duplicate | RSN-03-T02, RSN-03-D06 |
| 03 | AC 3 inactive · AC 4 status untouched | RSN-03-T03, RSN-03-D08 |
| 03 | AC 5 not found (D5) | RSN-03-D02 – D04, D07 |
| 03 | AC 6 stale | RSN-03-T04, RSN-03-D09, D10, D13 |
| 03 | AC 7 permission | RSN-03-T05, RSN-03-D11 |
| 04 | AC 1 deactivate, history kept | RSN-04-T01, RSN-04-D04 |
| 04 | AC 2, 3 not in Wastage Entry | RSN-04-T02 |
| 04 | AC 4 cancel | RSN-04-T03 |
| 04 | AC 5 permission | RSN-04-T04, RSN-04-D09 |
| 04 | AC 6 not found (D6) | RSN-04-D03 |
| 04 | AC 7 delete blocked | RSN-04-D11, D12 |
| 04 | AC 8 failure | RSN-04-T05, RSN-04-D14 |

## Endpoint checklist (developer)

| Endpoint | Auth | Company isolation | SuperAdmin | Permission | Validation | Rule bypass | Over-posting | Concurrency | Injection | Error hygiene |
|---|---|---|---|---|---|---|---|---|---|---|
| `GetWarehouseReason` | 01-D01, D02 | 01-D03, D04 | 01-D06 | n/a — read | 01-D07 | 01-D05 | 01-D07 | n/a — read | 01-D07 | 01-D08 |
| `GetWestageReasonById` | 03-D01 | 03-D02, D03 | 03-D12 | n/a — read | 03-D05 | 03-D04 | n/a | n/a | 03-D05 | 03-D05 |
| `AddEditResonMaster` | 02-D01, D02 | 02-D03, 03-D07 | 02-D13 | 02-D12, 03-D11 | 02-D06, D07, D08 | 03-D08 | 02-D05 | 02-D11, 03-D09, D10 | 02-D06 | 02-D14 |
| `ValidateWastageReason` | 02-D15 | 02-D16 | 02-D13 | n/a — read | 02-D17 | n/a | n/a | n/a | 02-D17 | 02-D17 |
| `WestageReasonActive` | 04-D01 | 04-D02, D03 | 04-D10 | 04-D09 | 04-D07 | 04-D05 | 04-D08 | 04-D13 | n/a — int / bool | 04-D14 |
| `DeleteWastageById` (blocked) | n/a — refused for everyone | 04-D12 | 04-D10 | n/a | n/a | 04-D11 | n/a | n/a | n/a | n/a |

## Files

| Use case | Tester | Developer |
|---|---|---|
| 01 | [`01-view-and-search-reasons.tester.md`](./01-view-and-search-reasons.tester.md) | [`01-view-and-search-reasons.developer.md`](./01-view-and-search-reasons.developer.md) |
| 02 | [`02-add-reason.tester.md`](./02-add-reason.tester.md) | [`02-add-reason.developer.md`](./02-add-reason.developer.md) |
| 03 | [`03-rename-reason.tester.md`](./03-rename-reason.tester.md) | [`03-rename-reason.developer.md`](./03-rename-reason.developer.md) |
| 04 | [`04-switch-reason-on-off.tester.md`](./04-switch-reason-on-off.tester.md) | [`04-switch-reason-on-off.developer.md`](./04-switch-reason-on-off.developer.md) |
