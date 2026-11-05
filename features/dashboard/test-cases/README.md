# Test cases — Dashboard

Feature code `DSH`. Fixtures: CO-A, locations CK and O1 (U1 mapped to both), O2 (U1 not mapped). ONION at CK 2 kg + legacy row 500 g (reorder 5, ₹40 / kg); TOMATO at O1 0 kg; 20 healthy items. 7 pending requisitions from CK; 6 transfers in transit to O1, created by another user. POs: 1 draft, 2 submitted (₹5,000 and ₹7,000), 1 fully received. Stock count at CK today at 15:30. 3 bills at CK today (₹100 paid, ₹200 paid, ₹300 unpaid), 1 at O2 (₹999). U-ADMIN has everything; U-STAFF has `view_dashboard` only; CO-B has its own data.

| Files | |
|---|---|
| [`dashboard.tester.md`](./dashboard.tester.md) | UI cases |
| [`dashboard.developer.md`](./dashboard.developer.md) | API cases |
