# 05 — Set the default location — developer test cases

- Use case: [`../05-default-location.md`](../05-default-location.md)
- API: [`../api-contract.md`](../api-contract.md#save-location) · response shapes: [`project-overview.md` → API conventions](../../../project-overview.md#api-conventions)
- Data model: [`../data-model.md`](../data-model.md)
- Fixtures and tokens: [`README.md`](./README.md#test-data)

Defaults: `Origin: HOST-A`, `Authorization: Bearer TOKEN_A_ADMIN`. "Save" = `POST /Setting/SaveWareHouseDetails` with the location's current values (including `UpdatedAt` from a fresh Get) plus the change.

## Test cases

| ID | Category | Caller (token · Origin) | Request (what is manipulated) | Expected result |
|---|---|---|---|---|
| `LOC-05-D01` | Company isolation / rule | default | Save LOC-A2 with `"IsDefault": true` | `status: true`. In CO-A only LOC-A2 has `IsDefault = 1` (LOC-A1 now 0). **LOC-B1 still `IsDefault = 1`** (TC-10) |
| `LOC-05-D02` | Rule bypass | default | Save LOC-A3 (inactive) with `"IsDefault": true` and its current `UpdatedAt` | `status: false`, "Activate the location to make it default"; LOC-A3 unchanged; LOC-A1 keeps `IsDefault = 1`; CO-A has no row with `IsActive = 0 AND IsDefault = 1` (LOC-Q3) |
| `LOC-05-D03` | Concurrency | default | Save LOC-A1 and LOC-A2, both with `"IsDefault": true`, in parallel, 10 rounds | After every round, exactly one CO-A row has `IsDefault = 1` (`SELECT COUNT(*) FROM Warehouse WHERE CompanyId = <CO-A> AND IsDefault = 1 AND IsDeleted = 0` = 1) |
