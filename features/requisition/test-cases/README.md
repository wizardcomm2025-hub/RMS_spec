# Test cases — Requisition

Feature code `REQ`. Fixtures: CO-A locations K1 (kitchen, user U1 mapped), K2 (not mapped), CS (central store, `CanTakeRequisition`); items ONION (main kg, alt g, mapped to K1), RICE (K1); pending P1 (K1 → CS), approved A1, in-transit T1; CO-B requisition RB. U1 has create / edit / delete / view; U-APP also has approve; U-STAFF none.

| Files | |
|---|---|
| [`requisition.tester.md`](./requisition.tester.md) | UI cases |
| [`requisition.developer.md`](./requisition.developer.md) | API cases |
