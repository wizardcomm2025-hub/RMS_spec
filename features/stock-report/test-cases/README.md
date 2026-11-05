# Test cases — Stock Report

Feature code `SRP`. Fixtures: CO-A, locations CK and O1 (U1 mapped to both), O2 (U1 not mapped). ONION (raw, main kg, alt g, conversion 1000, reorder 5, last purchase ₹40/kg): CK row 2 kg + legacy CK row 500 g, O1 row 10 kg (ONION not mapped to O1), O2 row 7 kg. TOMATO mapped to CK, no stock row. CO-B items. U-STAFF without `view_stock_report`.

| Files | |
|---|---|
| [`stock-report.tester.md`](./stock-report.tester.md) | UI cases |
| [`stock-report.developer.md`](./stock-report.developer.md) | API cases |
