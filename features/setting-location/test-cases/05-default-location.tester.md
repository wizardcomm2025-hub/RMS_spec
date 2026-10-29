# 05 — Set the default location — tester test cases

- Use case: [`../05-default-location.md`](../05-default-location.md)
- Screen design: [`../ui-ux.md`](../ui-ux.md)
- Fixtures: [`README.md`](./README.md#test-data)

All cases: log in as A-ADMIN on HOST-A and open Setting → Location, unless stated.

## Test cases

| ID | Covers | Precondition | Steps | Expected result |
|---|---|---|---|---|
| `LOC-05-T01` | AC 1 | Kitchen is Default | Edit Outlet One; tick **Default Location**; Save changes. | "Location updated". Outlet One shows the **Default** badge; Kitchen no longer does. Only one Default badge in the list. |
| `LOC-05-T02` | AC 3 | T01 done | Edit Outlet One; untick Default Location; save. | "Location updated". No row has the Default badge. |
| `LOC-05-T03` | AC 4 | Old Store is inactive | Edit Old Store; try to tick Default Location. | The checkbox is disabled with "Activate the location to make it default". |
| `LOC-05-T04` | AC 1 + 04 | Outlet One is Default | Deactivate Outlet One (confirm). | Outlet One shows **Inactive** and loses the Default badge. |
| `LOC-05-T05` | AC 2 | — | Log in as B-ADMIN on HOST-B after T01. | CO-B's Kitchen still has its **Default** badge. |
