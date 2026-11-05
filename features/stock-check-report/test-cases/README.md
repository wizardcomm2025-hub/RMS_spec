# Test cases — Stock Check Report

Feature code `SCR`. Fixtures: CO-A, locations CK and O1 (U1 mapped to both), O2 (U1 not mapped). ONION (raw, main kg, alt g, conversion 1000, cost ₹40/kg): counted at CK today 06:30 PM 12.5 kg with note "Rotten removed" (date saved with time), at CK today 10:00 AM 10 kg, at CK on 01/Nov 500 g (legacy row in g, date saved at midnight, no time), at O2 today 7 kg. TOMATO: counted at CK today 0 kg by U1. GARLIC: created today, never counted (zero row from item creation), mapped to CK. BEANS: counted at O1, since unmapped. SALT: legacy count in "box", which is neither its main nor its alternate unit. CO-B counts. U-STAFF without `view_stock_check_report`.

| Files | |
|---|---|
| [`stock-check-report.tester.md`](./stock-check-report.tester.md) | UI cases |
| [`stock-check-report.developer.md`](./stock-check-report.developer.md) | API cases |
