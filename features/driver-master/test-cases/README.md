# Test Cases — Driver Master (`DRV`)

Test suite for Setting → Driver Master.

---

## 1. Test accounts & personas

| ID | Persona | Role & Permissions | Used for |
|---|---|---|---|
| `A-FULL` | Admin / Dispatch Lead | Full permissions on `DriverMaster` (`Create`, `Edit`, `Delete`, `ViewList`, `ViewDetails`) | Happy path, add, edit, status switch |
| `A-VIEW` | Auditor / Read-only User | Only `ViewList` and `ViewDetails` on `DriverMaster` | Permission boundary testing (buttons hidden; API returns the permission message) |
| `A-OTHER` | Tenant B Admin | Full permissions on Company 2 | Multi-tenancy isolation |
| `SUPER` | SuperAdmin | Global SuperAdmin token | SuperAdmin isolation tests |

---

## Test data

Two companies on two hosts in the **same Tenant DB**. Tokens and headers as in Setting → Location: `TOKEN_A_ADMIN` (= A-FULL), `TOKEN_A_ADMIN2` (second CO-A admin), `TOKEN_A_VIEW` (= A-VIEW), `TOKEN_B_ADMIN` (= A-OTHER), `TOKEN_SA` (= SUPER), `TOKEN_EXPIRED`, `TOKEN_TAMPERED`; default `Origin: HOST-A` (CO-A), `HOST-B` = CO-B. Extra fixture: `DRV_B` — CO-B driver "Kiran Rao", licence `KA-0520210077777`, active. Expected API results follow `project-overview.md` → API conventions: a missing permission is HTTP 200 with `status: false` and "You don't have permission to perform this action." (never 403).

## 2. Test data fixtures (Company 1)

| Variable | Driver Name | License Number | Profile Image | Status | Notes |
|---|---|---|---|---|---|
| `DRV_1` | Rajesh Kumar | `DL-1420110012345` | `avatar1.jpg` | Active | Primary active driver |
| `DRV_2` | Amit Singh | `MH-0220190098765` | NULL | Active | Active driver without photo |
| `DRV_3` | Suresh Sharma | `KA-0320150043210` | `avatar3.jpg` | Inactive | Inactive driver |

---

## 3. Test suites

| Suite | Use case | Developer tests | Tester tests |
|---|---|---|---|
| `DRV-01` | View & Search Drivers | [`01-view-and-search-drivers.developer.md`](./01-view-and-search-drivers.developer.md) | [`01-view-and-search-drivers.tester.md`](./01-view-and-search-drivers.tester.md) |
| `DRV-02` | Add Driver | [`02-add-driver.developer.md`](./02-add-driver.developer.md) | [`02-add-driver.tester.md`](./02-add-driver.tester.md) |
| `DRV-03` | Edit Driver | [`03-edit-driver.developer.md`](./03-edit-driver.developer.md) | [`03-edit-driver.tester.md`](./03-edit-driver.tester.md) |
| `DRV-04` | Switch Driver On / Off | [`04-switch-driver-on-off.developer.md`](./04-switch-driver-on-off.developer.md) | [`04-switch-driver-on-off.tester.md`](./04-switch-driver-on-off.tester.md) |
