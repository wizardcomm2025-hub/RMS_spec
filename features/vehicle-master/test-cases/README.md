# Test cases — Vehicle Master

Derived from the use cases, [`../data-model.md`](../data-model.md), [`../api-contract.md`](../api-contract.md) and [`../ui-ux.md`](../ui-ux.md). Rules: [`../../../conventions.md`](../../../conventions.md) → "Test cases". Results go by test ID in `RMS_API/specs/vehicle-master/test-results.md`.

Feature code: `VEH`

## Test data

Two companies on two hosts in the **same Tenant DB**.

| Fixture | State |
|---|---|
| `CO-A` / `HOST-A`, `CO-B` / `HOST-B` | Two distinct tenant companies in the same Tenant DB |
| `A-ADMIN`, `A-ADMIN2` | CO-A users with Vehicle Master view, `create_vehicle_master`, `edit_vehicle_master` |
| `A-VIEW` | CO-A user who can open Vehicle Master but has no create / edit permissions |
| `B-ADMIN`, `SA` | CO-B admin; SuperAdmin |
| `VEH-VAN-1` | CO-A "MH12AB1234", "Toyota HiAce", active |
| `VEH-TRUCK-1` | CO-A "MH12CD5678", "Tata 407", active |
| `VEH-CAR-1` | CO-A "DL01EF9999", "Mahindra Bolero", active |
| `VEH-OLD` | CO-A "MH14ZZ0001", "Old Van", **inactive** |
| `VEH-A-MORE` | 6 more active CO-A vehicles (total 10 non-deleted vehicles: 9 active + 1 inactive) |
| `VEH-B-VAN` | CO-B "KA01AB1111", "Force Traveller", active |

Tokens, headers and default GUIDs (`TOKEN_A_ADMIN`, `TOKEN_A_ADMIN2`, `TOKEN_A_VIEW`, `TOKEN_B_ADMIN`, `TOKEN_SA`, `TOKEN_EXPIRED`, `TOKEN_TAMPERED`; default `Origin: HOST-A`).

## Coverage

| Use case | Covers | Test IDs |
|---|---|---|
| 01 | AC 1 all 10, sorted, company only | VEH-01-T01, VEH-01-D04 |
| 01 | AC 2 inactive chip | VEH-01-T02 |
| 01 | AC 3 search registration + status | VEH-01-T03 |
| 01 | AC 4 search manufacturer (D5) | VEH-01-T04 |
| 01 | AC 5 no match & clear | VEH-01-T05 |
| 01 | AC 6 empty state | VEH-01-T06 |
| 01 | AC 7 load error & retry | VEH-01-T07, VEH-01-D08 |
| 01 | AC 8 no pagination | VEH-01-T08, VEH-01-D07 |
| 01 | AC 9 Add button hidden | VEH-01-T09 |
| 01 | AC 10 Page header (no duplicate description) | VEH-01-T10 |
| 02 | AC 1 add vehicle | VEH-02-T01, VEH-02-D04 |
| 02 | AC 2 duplicate registration (incl. inactive) | VEH-02-T02, T03, VEH-02-D08, D09 |
| 02 | AC 3 other company registration allowed | VEH-02-T04, VEH-02-D10 |
| 02 | AC 4 registration characters (letters/digits only) | VEH-02-T05, VEH-02-D06 |
| 02 | AC 5 manufacturer name validation | VEH-02-T06, VEH-02-D07 |
| 02 | AC 6 duplicate registration live validation | VEH-02-T02, VEH-02-D15–D17 |
| 02 | AC 7 over-posting ignored | VEH-02-D05 |
| 02 | AC 8 permission check | VEH-02-T07, VEH-02-D12 |
| 02 | Alt: discard, double submit, failure | VEH-02-T08, T09, VEH-02-D11, D14 |
| 03 | AC 1 edit vehicle | VEH-03-T01 |
| 03 | AC 2 duplicate registration on edit | VEH-03-T02, VEH-03-D06 |
| 03 | AC 3 inactive vehicle edit | VEH-03-T03 |
| 03 | AC 4 status not overwritten by save | VEH-03-D08 |
| 03 | AC 5 other company / deleted vehicle (D4) | VEH-03-D02–D04 |
| 03 | AC 6 stale concurrency check | VEH-03-T04, VEH-03-D09, D10 |
| 03 | AC 7 permission check | VEH-03-T05, VEH-03-D11 |
| 04 | AC 1 deactivate vehicle | VEH-04-T01, VEH-04-D04 |
| 04 | AC 2 hidden in dispatch dropdowns | VEH-04-T02 |
| 04 | AC 3 reactivate vehicle | VEH-04-T03 |
| 04 | AC 4 cancel confirmation | VEH-04-T04 |
| 04 | AC 5 permission check | VEH-04-T05, VEH-04-D10 |
| 04 | AC 6 other company vehicle | VEH-04-D03 |
| 04 | AC 7 hard delete blocked (D1) | VEH-04-D12, D13 |
| 04 | Alt: server error reverts switch | VEH-04-T06, VEH-04-D14 |

## Endpoint checklist (developer)

| Endpoint | Auth | Company isolation | SuperAdmin | Permission | Validation | Rule bypass | Over-posting | Concurrency | Injection | Error hygiene |
|---|---|---|---|---|---|---|---|---|---|---|
| `GetVechileList` | 01-D01, D02 | 01-D03, D04 | 01-D05 | n/a — read | 01-D06 | n/a | 01-D06 | n/a — read | 01-D06 | 01-D08 |
| `GetVehicleById` | 03-D01 | 03-D02, D03 | 03-D12 | n/a — read | 03-D05 | 03-D04 | 03-D03 | n/a | 03-D05 | 03-D05 |
| `AddEditVechile` | 02-D01, D02 | 02-D03, 03-D07 | 02-D13 | 02-D12, 03-D11 | 02-D06–D09 | 03-D08 | 02-D05 | 02-D11, 03-D09, D10 | 02-D06 | 02-D14 |
| `ValidateRegNo` | 02-D15 | 02-D16 | 02-D13 | n/a — read | 02-D17 | n/a | n/a | n/a | 02-D17 | 02-D17 |
| `ChangeVehicleStatus` | 04-D01 | 04-D02, D03 | 04-D11 | 04-D10 | 04-D08 | 04-D05 | 04-D09 | 04-D14 | n/a — bool | 04-D15 |
| `DeleteVehicle` (blocked) | n/a — refused | 04-D13 | 04-D11 | n/a | n/a | 04-D12 | n/a | n/a | n/a | n/a |

## Files

| Use case | Tester | Developer |
|---|---|---|
| 01 | [`01-view-and-search-vehicles.tester.md`](./01-view-and-search-vehicles.tester.md) | [`01-view-and-search-vehicles.developer.md`](./01-view-and-search-vehicles.developer.md) |
| 02 | [`02-add-vehicle.tester.md`](./02-add-vehicle.tester.md) | [`02-add-vehicle.developer.md`](./02-add-vehicle.developer.md) |
| 03 | [`03-edit-vehicle.tester.md`](./03-edit-vehicle.tester.md) | [`03-edit-vehicle.developer.md`](./03-edit-vehicle.developer.md) |
| 04 | [`04-switch-vehicle-on-off.tester.md`](./04-switch-vehicle-on-off.tester.md) | [`04-switch-vehicle-on-off.developer.md`](./04-switch-vehicle-on-off.developer.md) |
