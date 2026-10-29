# Test cases — Brand and Sub Brand

Feature code `BRD`. Results in `RMS_API/specs/brands/test-results.md`; manual run at the end of the project.

## Test data

| Fixture | State |
|---|---|
| CO-A / HOST-A, CO-B / HOST-B; A-ADMIN, A-ADMIN2, A-VIEW, B-ADMIN, SA; tokens | As in Setting → Location (A-ADMIN has all four brand permissions; A-VIEW none) |
| BRD-COKE | CO-A brand "Coca-Cola", active; sub-brands SB-THUMS (active, used by active raw material RM-THUMS) and SB-DIET (active, unused) |
| BRD-AMUL | CO-A brand "Amul", active, no sub-brands, used by active raw material RM-BUTTER |
| BRD-FREE | CO-A brand "Fortune", active, no sub-brands, unused |
| BRD-OLD | CO-A brand "Old Brand", **inactive**, with inactive sub-brand SB-OLD |
| BRD-B | CO-B brand "Pepsi" with sub-brand SB-B-7UP |

## Files

| Use case | Tester | Developer |
|---|---|---|
| 01–03 Brand | [`brand.tester.md`](./brand.tester.md) | [`brand.developer.md`](./brand.developer.md) |
| 04–06 Sub Brand | [`sub-brand.tester.md`](./sub-brand.tester.md) | [`sub-brand.developer.md`](./sub-brand.developer.md) |
