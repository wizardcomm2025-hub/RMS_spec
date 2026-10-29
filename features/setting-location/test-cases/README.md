# Test cases — Setting → Location

The test cases for this feature, derived from its use cases, [`../data-model.md`](../data-model.md), [`../api-contract.md`](../api-contract.md) and [`../ui-ux.md`](../ui-ux.md). The rules are in [`../../../conventions.md`](../../../conventions.md) under "Test cases".

- **Tester** (`NN-<use-case>.tester.md`): what a user can do through the admin dashboard. No endpoints, request bodies or SQL.
- **Developer** (`NN-<use-case>.developer.md`): direct API calls with no / bad tokens, another company's IDs or host, garbage input, extra fields and double submits, with the database state checked after each call.

Actual results are recorded by test ID in `RMS_API/specs/setting-location/test-results.md`, not here.

Feature code for test IDs: `LOC`

> **Phase note (2026-10-28).** The UI/UX phase is built; the API/DB phase is not. Cases that depend on the new API behaviour (status switch, `IsActive`, validation messages, auth on get-by-id) will fail until that phase lands. All LOC open questions were resolved on 2026-10-28, so no case is blocked; see [`../README.md`](../README.md#cross-cutting-open-questions).

## Test data

Two companies on two hosts, **in the same Tenant DB**, so that a missing `CompanyId` filter shows up.

| Fixture | State |
|---|---|
| CO-A | Company A on host HOST-A. Location types: Warehouse, Outlet, Central Kitchen |
| CO-B | Company B on host HOST-B, same Tenant DB as CO-A. Location types: Warehouse, Outlet, **Commissary** (a type CO-A does not have) |
| CO-C | Company C on host HOST-C with **no locations** |
| CO-P | Company P on host HOST-P with **30 active locations** (pagination) |
| A-ADMIN | CO-A user whose role has Location view, Create and Edit |
| A-ADMIN2 | A second CO-A user with the same role (ownership tests) |
| A-VIEW | CO-A user who can open Setting → Location but has **no** Location Create or Edit |
| B-ADMIN | CO-B user with Location view, Create and Edit |
| SA | SuperAdmin |
| EMP-A1, EMP-A2 | Active CO-A staff users |
| EMP-AX | Inactive CO-A user |
| EMP-B1 | Active CO-B staff user |
| LOC-A1 | CO-A "Kitchen", type Central Kitchen, **active, Default**, 2 bins, employees EMP-A1 + EMP-A2, Can Take Requisition = Yes, created by A-ADMIN |
| LOC-A2 | CO-A "Outlet One", type Outlet, active, POS outlet, not default; employees EMP-A2 and **EMP-AX** (mapped before EMP-AX was deactivated) |
| LOC-A3 | CO-A "Old Store", type Warehouse, **inactive** (`IsActive = 0, IsDeleted = 0`), employee EMP-A1 |
| LOC-AD | CO-A "Closed Depot", **deleted** (`IsDeleted = 1`) |
| LOC-B1 | CO-B "Kitchen", active, **Default** (same name as LOC-A1) |
| LOC-B2 | CO-B "Central Store", active (name not used in CO-A) |

Reset the fixtures before each run (script to be added with the Postman collection).

## Tokens and headers (developer)

| Name | How to get it |
|---|---|
| `TOKEN_A_ADMIN`, `TOKEN_A_ADMIN2`, `TOKEN_A_VIEW`, `TOKEN_B_ADMIN` | Log in as that user on its company host |
| `TOKEN_SA` | SuperAdmin login |
| `TOKEN_EXPIRED` | A-ADMIN token past its expiry |
| `TOKEN_TAMPERED` | `TOKEN_A_ADMIN` with one character of the signature changed |
| `TOKEN_GARBAGE` | The string `abc.def.ghi` |

Unless a case says otherwise, every developer request sends `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN` and `Content-Type: application/json`.

## Running the developer cases

- Postman collection and environment: `RMS_API/specs/setting-location/postman/` (to be created in the API phase), one request per developer test ID.
- Run: `postman collection run <collection.json> -e <environment.json>` (Postman CLI is installed on the dev machine).
- After each write case, run the SQL check named in the case against the Tenant DB, for example `SELECT WarehouseId, CompanyId, WarehouseName, IsActive, IsDeleted, IsDefault, IsPOSOutlet, UserResponsible, CreatedBy, UpdatedBy FROM dbo.Warehouse WHERE CompanyId IN (<CO-A>, <CO-B>)`, and the row counts of `BinWarehouse` and `UserWarehouseMapping`.
- "Nothing written" means: the same `Warehouse`, `BinWarehouse` and `UserWarehouseMapping` rows and values as before the call.

## Coverage

| Use case | Covers | Test IDs | Migrated from |
|---|---|---|---|
| 01 | AC 1 list, order, company only | LOC-01-T01, LOC-01-D04 | — |
| 01 | AC 2 inactive shown | LOC-01-T02, LOC-01-D09 | — |
| 01 | AC 3 combined filters | LOC-01-T03 | TC-18 |
| 01 | AC 4 no match | LOC-01-T04 | TC-23 |
| 01 | AC 5 empty | LOC-01-T05 | TC-23 |
| 01 | AC 6 load error + Retry | LOC-01-T06 | TC-19, TC-23 |
| 01 | AC 7 generic failure response | LOC-01-D11, D12 | TC-19 |
| 01 | AC 8 pagination | LOC-01-T07 | — |
| 01 | AC 9 Add hidden | LOC-01-T08 | TC-17 |
| 01 | AC 11 other screens get active only | LOC-01-D09 | — |
| 01 | AC 10 / UI AC 1, 2, 4, 5 | LOC-01-T10 – T14 | TC-22, TC-25 |
| — | UI AC 3 (no hex colours) | Code check, not a test case — recorded in `test-results.md` | TC-24 |
| 01 | Alt: deleted rows never shown | LOC-01-D09 | — |
| 02 | AC 1 add | LOC-02-T01 | — |
| 02 | AC 2 POS | LOC-02-D06 | TC-1, TC-2 |
| 02 | AC 3 bins | LOC-02-D07 | TC-8 |
| 02 | AC 4 duplicate / trim | LOC-02-T03 – T06, LOC-02-D12, D13 | TC-5, TC-11 |
| 02 | AC 5 length | LOC-02-T07, LOC-02-D10, D11 | TC-12 |
| 02 | AC 6 pincode | LOC-02-T10, LOC-02-D14 | TC-13 |
| 02 | AC 7 over-posting | LOC-02-D04, D05 | — |
| 02 | AC 8 no POS field | LOC-02-T02 | TC-1 |
| 02 | AC 9 permission | LOC-02-T12, LOC-02-D25 | TC-17 |
| 02 | AC 10 same name other company | LOC-02-T11, LOC-02-D28 | — |
| 02 | AC 11 invalid employee / type | LOC-02-D15, D17, D18 | — |
| 02 | AC 12 SuperAdmin refused | LOC-02-D30 | — |
| 02 | Alt: empty name, type, invalid type, employees, double click, discard, failure | LOC-02-T08, T09, T13 – T15, T18, LOC-02-D08, D09, D15 – D18, D24 | — |
| 03 | AC 1 edit | LOC-03-T01 | — |
| 03 | AC 2 duplicate on edit | LOC-03-T02, LOC-03-D09 | TC-3, TC-4 |
| 03 | AC 3 ownership | LOC-03-D12, D14 | TC-9 |
| 03 | AC 4 bins | LOC-03-D13 | TC-8 |
| 03 | AC 5 401 | LOC-03-D01 | TC-6 |
| 03 | AC 6 not found | LOC-03-D02 | TC-7 |
| 03 | AC 7 other company | LOC-03-D03, D06, D10 | — |
| 03 | AC 8 POS | LOC-03-D16 | TC-2 |
| 03 | AC 9 permission | LOC-03-T05, LOC-03-D18 | TC-17 |
| 03 | AC 10 inactive edit | LOC-03-T04 | — |
| 03 | AC 11 stale save refused | LOC-03-T11, T12, LOC-03-D17, D21, D22, D24 | — |
| 03 | AC 12 not found on save | LOC-03-D10, D11 | — |
| 03 | Alt: same-name rename, already-mapped employees | LOC-03-T03, LOC-03-D19, D23 | — |
| 04 | AC 1 deactivate | LOC-04-T01, LOC-04-D09 | TC-14 |
| 04 | AC 2 hidden elsewhere | LOC-04-T02 | TC-15 |
| 04 | AC 3 reactivate | LOC-04-T03, LOC-04-D10 | TC-16 |
| 04 | AC 4 cancel | LOC-04-T04 | — |
| 04 | AC 5 no switch / no delete | LOC-04-T05, LOC-04-D15 | TC-17 |
| 04 | AC 6 other company | LOC-04-D04, D16 | — |
| 04 | AC 7 parallel calls | LOC-04-D12 | — |
| 04 | AC 8 legacy delete refused | LOC-04-D15, D16 | — |
| 05 | AC 1 one default | LOC-05-T01, LOC-05-D01 | TC-10 |
| 05 | AC 2 other company untouched | LOC-05-T05, LOC-05-D01 | TC-10 |
| 05 | AC 3 untick | LOC-05-T02 | — |
| 05 | AC 4 inactive disabled | LOC-05-T03, LOC-05-D02 | — |
| 05 | AC 5 never two defaults | LOC-05-D03 | — |
| 05 | AC 6 inactive default refused | LOC-05-D02 | — |
| — | Observation: profile screen for a user mapped to an inactive location (plan R2, out of scope) | — | TC-21 (not migrated; observation only, kept in test-results) |

## Endpoint checklist (developer)

| Endpoint | Auth | Company isolation | SuperAdmin boundary | Permission | Validation | Rule bypass | Over-posting | Concurrency | Injection | Error hygiene |
|---|---|---|---|---|---|---|---|---|---|---|
| `GetWareHouseDetails` | 01-D01–D03 | 01-D04–D07 | 01-D10 | n/a — read needs no Create/Edit | 01-D09 | 01-D09 | 01-D08 | n/a — read only | n/a — no input | 01-D12 |
| `GetAllLocationDDl` | 01-D13 | 01-D14 | 01-D10 | n/a — read only | n/a — no body | n/a | n/a — no body | n/a | n/a | 01-D11 |
| `GetWareHouseDetailsById` | 03-D01 | 03-D03, D06, D08 | 03-D20 | n/a — read needs no Create/Edit | 03-D02, D04, D05, D24 | n/a | 03-D06 | n/a | 03-D05 | 03-D05 |
| `SaveWareHouseDetails` | 02-D01–D03 | 02-D04, 03-D10 | 02-D30, 03-D20 | 02-D25, 03-D18 | 02-D08–D20, 03-D23 | 02-D06, D07, 03-D15, 03-D21, 05-D02 | 02-D04, D05, 03-D14 | 02-D24, 03-D17, 03-D21, 03-D22, 05-D03 | 02-D21–D23 | 02-D26 |
| `ValidateLocName` | 02-D27 | 02-D28 | 02-D30 | n/a — read only | 02-D29 | n/a | n/a | n/a | 02-D29 | 02-D29 |
| `ChangeWareHouseStatus` | 04-D01–D03 | 04-D04 | 04-D18 | 04-D14 | 04-D05–D08 | 04-D06 | 04-D13 | 04-D11, D12 | n/a — numeric / boolean only | 04-D17 |
| `GetUserWarehouse` | 02-D31 | 02-D32 | 02-D30 | n/a | n/a | n/a | n/a | n/a | n/a | n/a |
| `DeleteWareHouseDetails` (legacy, blocked) | covered by D15 with a valid token | 04-D16 | 04-D18 | n/a — always refused | n/a | 04-D15 | n/a | n/a | n/a | n/a |

## Files

| Use case | Tester | Developer |
|---|---|---|
| 01 | [`01-view-and-filter-locations.tester.md`](./01-view-and-filter-locations.tester.md) | [`01-view-and-filter-locations.developer.md`](./01-view-and-filter-locations.developer.md) |
| 02 | [`02-add-location.tester.md`](./02-add-location.tester.md) | [`02-add-location.developer.md`](./02-add-location.developer.md) |
| 03 | [`03-edit-location.tester.md`](./03-edit-location.tester.md) | [`03-edit-location.developer.md`](./03-edit-location.developer.md) |
| 04 | [`04-switch-location-on-off.tester.md`](./04-switch-location-on-off.tester.md) | [`04-switch-location-on-off.developer.md`](./04-switch-location-on-off.developer.md) |
| 05 | [`05-default-location.tester.md`](./05-default-location.tester.md) | [`05-default-location.developer.md`](./05-default-location.developer.md) |
