# Test cases — PO Receive

Feature code `PRC`. Fixtures: CO-A vendor V1; raw materials ONION (main kg, alt g, conversion 1000), OIL (main L); location L1; POs S1 (Submitted: ONION 10 kg @ 40, OIL 5 L @ 120), S2 (Submitted, fully received), D1 (Draft), C1 (Cancelled); CO-B PO with the **same number** as S1 (`PB`) and receive `RB`. U-RECV has only `receive_receive_asset` + `viewlist_receive_asset`; U-STAFF has none.

| Files | |
|---|---|
| [`po-receive.tester.md`](./po-receive.tester.md) | UI cases |
| [`po-receive.developer.md`](./po-receive.developer.md) | API cases |
