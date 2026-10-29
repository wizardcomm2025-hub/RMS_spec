# Test cases — {{FEATURE_NAME}}

The test cases for this feature, derived from its use cases, [`../data-model.md`](../data-model.md), [`../api-contract.md`](../api-contract.md) and [`../ui-ux.md`](../ui-ux.md). The rules are in [`../../../conventions.md`](../../../conventions.md) under "Test cases".

- **Tester** (`NN-<use-case>.tester.md`): what a user can do through the admin dashboard, tested manually or with UI automation. No endpoints or request bodies.
- **Developer** (`NN-<use-case>.developer.md`): what a direct or manipulated API request can do — no token, another company's IDs or host, a company user on SuperAdmin endpoints, garbage input, extra fields, double submits.

Actual results are **not** recorded here. Record them by test ID in `RMS_API/specs/<feature-name>/test-results.md`.

Feature code for test IDs: `{{FEATURE_CODE}}`

## Test data

<!-- TODO: shared fixtures, each described by its state, never by hard-coded IDs. Standard RMS fixtures (keep these names across features):
| CO-A | Company A on host HOST-A, in the shared Tenant DB |
| CO-B | Company B on host HOST-B, in the same Tenant DB as CO-A (proves CompanyId filtering) |
| A-ADMIN | CO-A user whose role has every permission of this module |
| A-LIMITED | CO-A user whose role lacks this module's Create / Edit permissions |
| B-ADMIN | CO-B user with every permission of this module |
| SA | SuperAdmin -->

| Fixture | State |
|---|---|
| <name> | <state> |

## Tokens and headers (developer)

| Name | How to get it |
|---|---|
| `TOKEN_A_ADMIN` | Log in as A-ADMIN on HOST-A |
| `TOKEN_EXPIRED` | A token of A-ADMIN past its expiry |
| `TOKEN_TAMPERED` | `TOKEN_A_ADMIN` with one character of the signature changed |

Every developer request sends `Origin: <host>` (the tenant comes from it) and `Authorization: Bearer <token>` unless the case says otherwise.

## Running the developer cases

<!-- TODO: the Postman collection path (in RMS_API/specs/<feature>/), environment variables, and the command, e.g. `postman collection run <file> -e <env>`. SQL checks to run after each case. -->

## Coverage

<!-- TODO: every acceptance criterion (AC) and alternative flow of every use case, with the test IDs that cover it. A row with no test ID is a gap. -->

| Use case | Covers | Test IDs |
|---|---|---|
| 01 | AC 1 | `{{FEATURE_CODE}}-01-T01` |

## Endpoint checklist (developer)

<!-- TODO: one row per endpoint. Each cell holds the developer test IDs for that category, or `n/a` with a short reason. -->

| Endpoint | Auth | Company isolation | SuperAdmin boundary | Permission | Validation | Rule bypass | Over-posting | Concurrency | Injection | Error hygiene |
|---|---|---|---|---|---|---|---|---|---|---|
| `POST /<Controller>/<Method>` | | | | | | | | | | |

## Files

| Use case | Tester | Developer |
|---|---|---|
| 01 | [`01-<use-case>.tester.md`](./01-<use-case>.tester.md) | [`01-<use-case>.developer.md`](./01-<use-case>.developer.md) |

<!-- For a system-only use case, write `n/a — no UI` in the Tester column. -->
