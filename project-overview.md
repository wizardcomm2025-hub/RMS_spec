# RMS — Project Overview

> **Purpose of this file:** starting-point context for writing feature specifications and test cases. Read this before drafting any spec, use case, data model or test case. Update it when fundamental product decisions change.

> **Status.** The architecture and API-convention sections are filled in from the code (2026-10-28). Sections marked `TODO` are filled through a `spec-intake` interview — do not guess them. The glossary is the single source of truth for shared terms.

---

## What the product is

RMS is a multi-company **Restaurant Management System**. Each company (a restaurant business) gets its own site (hostname) and runs its operations from an admin dashboard: locations (outlets, warehouses, central kitchens), inventory and stock items, menus and recipes, requisitions, purchase orders and receiving, wastage, staff (stewards, attendance, rosters, shifts, leave), users and roles, orders and settings. A SuperAdmin control plane onboards companies, assigns plans and menus, and handles billing.

<!-- TODO: confirm and extend from the project-overview interview: who the customers are, what problem RMS solves for them, the customer storefront's role. -->

---

## The user roles

Users belong to one company. What a user may do is set by their **role**, and each role has per-menu permissions (Create / Edit / Delete) managed in User Management → Roles. The admin dashboard shows or hides actions from these permissions.

| Role | Where it lives | Notes |
|---|---|---|
| Company user (any role) | Tenant DB, scoped by `CompanyId` | Logs in on the company's own host with username + password |
| SuperAdmin | Central DB | Logs in through `/Users/SuperAdminLogin`; uses `/SuperAdmin/*` endpoints (no tenant) |
| Support user | <!-- TODO: confirm where support users are stored --> | Can log in *as* a company through `/Users/SupportLoginAsCompany` |

<!-- TODO: list the standard company roles (e.g. Admin, Manager, Steward…) and what each may do. Fill from the project-overview interview. -->

---

## Core features

Each module has a plain-language section in `../RMS_API/specs/MODULES.md`. Modules with a formal spec here:

- **Setting → Location** — see [features/setting-location/](./features/setting-location/).
- **Setting → Category Master** — see [features/category-master/](./features/category-master/).
- **Setting → Department** — see [features/department/](./features/department/).
- **Setting → Reason of Wastage** — see [features/reason-of-wastage/](./features/reason-of-wastage/).
- **Setting → Vehicle Master** — see [features/vehicle-master/](./features/vehicle-master/).
- **Setting → Driver Master** — see [features/driver-master/](./features/driver-master/).

<!-- TODO: add one line per module as it gets a spec here. -->

---

## Platforms

| Surface | What it does | Technology |
|---|---|---|
| RMS API (`D:\RMS\RMS_API`) | HTTP API for the admin dashboard and the SuperAdmin control plane | ASP.NET Core Web API, .NET 6, JWT bearer, log4net |
| Tenant DB | Company data: locations, stock, menus, users, roles, orders… Several companies can share one Tenant DB | SQL Server, stored procedures |
| Central DB | Control plane: companies, site registry (host → company + database), plans, menus per plan, SuperAdmin, billing | SQL Server, stored procedures |
| Integrations | Razorpay (payments, onboarding, webhooks), PayU (webhooks), Tally (accounting export), OCR | Separate projects in `RMS_API` (`Integration.*`) |
| Admin dashboard (`D:\RMS\RMS_next_js\RMS_NextJS`) | Operational screens for company users | Next.js 15, React 19, MUI 7 (Bazaar template), axios, react-hook-form + yup |

---

## Folder structure

```
D:\RMS\
├── App-Specs\                 ← this folder; feature specs, test cases, ADRs
├── RMS_API\                   ← API + SQL scripts; reads specs at ../App-Specs/
│   └── specs\                 ← plan.md / tasks.md / test-results.md per feature,
│                                UI_UX_STANDARDS.md, MODULES.md
└── RMS_next_js\RMS_NextJS\    ← admin dashboard; reads specs at ../../App-Specs/
```

SQL source: `RMS_API/ProductManagement.API/Database Scripts/` (`Stored Procedure/`, `Table/`, `Tenant/`, `CentralDB/`, `Function/`, `UserDefinedFunction/`) and DB dumps the user provides on request.

---

## API conventions

These are facts from the code (2026-10-28). Developer test cases use them as expected results. If the code changes, update this table first.

### Request routing

- Every endpoint is `POST /{Controller}/{Method}` with a JSON body (or none).
- The tenant is resolved from the **`Origin` header** (`MultiTenancy/TenantResolutionMiddleware.cs`): one host → one company + its Tenant DB. These routes skip tenant resolution: `/superadmin*`, `/users/superadminlogin`, `/users/supportloginascompany`, the Razorpay/PayU webhooks and callbacks, `/swagger`.
- `CompanyId` and `UserId` used by services come from the JWT (`HttpContext.Items["User"]`), not from the body.

### Response shapes

| Situation | Produced by | HTTP | Body |
|---|---|---|---|
| Success | Controller | 200 | `{ "status": true, "message": "", "data": … }` |
| Business failure (validation, duplicate, not found) | Controller / service | 200 | `{ "status": false, "message": "<text>", "data": null }` |
| Unhandled exception | Controller `catch` | 200 | `{ "status": false, "message": <exception text> }` — see Known gaps |
| No / invalid / expired token on `[Authorize]` | `Helpers/AuthorizeAttribute.cs` | 401 | `{ "message": "Unauthorized" }` |
| Non-SuperAdmin on `[SuperAdminAuthorize]` | `Helpers/SuperAdminAuthorizeAttribute.cs` | 401 | `{ "status": false, "message": "Unauthorized: SuperAdmin authorization required." }` |
| `Origin` not registered | Tenant middleware | 404 | `{ "status": false, "message": "Site not registered.", "data": null }` |
| Site inactive | Tenant middleware | 403 | `{ "status": false, "message": "Site is inactive.", "data": null }` |
| Token's company ≠ host's company | Tenant middleware | 401 | `{ "status": false, "message": "Token does not match this site's company.", "data": null }` |

### Known gaps (as of 2026-10-28)

- Most controller `catch` blocks return `ex.Message` to the caller. Developer "Error hygiene" tests expect **no SQL, stack trace or internal names** in the message; a test that sees one fails until the endpoint is fixed. Target text, first decided for Location (LOC-Q2, 2026-10-28): "Something went wrong. Please try again."
- Menu permissions (`Location.Create`, `Location.Edit`, …) are enforced in the UI only today. No server-side permission check was found on the Setting endpoints. Location decided the API must enforce them (LOC-Q1, 2026-10-28; message "You don't have permission to perform this action."); other features decide in their own spec.

---

## Glossary

| Term | Meaning |
|---|---|
| Company | One customer business; identified by `CompanyId`. The data boundary for every tenant query. |
| Site / host | The hostname a company's users open (for example `cust1.rmsv2.gob.com`). Registered in the Central DB `SiteRegistry`; resolves to one company and one Tenant DB. |
| Central DB | The control-plane database: companies, site registry, plans, SuperAdmin. |
| Tenant DB | The database holding company data. Can be shared by several companies; rows carry `CompanyId`. |
| SuperAdmin | A platform operator who manages companies through `/SuperAdmin/*`. Not a company user. |
| Location | A physical place a company operates (outlet, warehouse, central kitchen). Stored as **Warehouse** (`dbo.Warehouse`); the UI calls it Location. |
| Location Type | A company's category for locations (for example Outlet, Warehouse); from `Map_Company_LocationType`. |
| Bin | A storage slot inside a location (`BinWarehouse`). |
| Category | A group of inventory items within one inventory type, stored as **StockGroup** (`dbo.StockGroup`, `ClassificationId = 1`). Managed in Setting → Category Master (formerly "Item Category"). |
| Main category / Sub-category | Two levels only: a main category has no parent; a sub-category's parent is a main category of the same type and company. |
| Category code | A short code (1–10 upper-case letters or digits) shown on stock and food-cost reports; `StockGroup.StockGroupAlias`. |
| Inventory type | A company's kind of stock (Raw Material, Semi-Finished, Finished Goods…), `dbo.InventoryType`. |
| Asset category | `StockGroup` row with `ClassificationId = 2`, used by asset management — not an item category. |
| Department | A team staff belong to (Kitchen, Service, Bar), `dbo.DepartmentMaster`, per company. Users have one; roster patterns and rosters are planned per department. Managed in Setting → Department. |
| Reason of Wastage | A reason staff pick when they write off stock (Expired, Spoiled), `dbo.WastageReasonMaster`, per company. Each `Wastage` line keeps its reason; switched-off reasons can't be picked. Managed in Setting → Reason of Wastage. |
| Vehicle | A company car, van or truck used to dispatch stock, `dbo.VehicleMaster`, per company; registration number unique. Managed in Setting → Vehicle Master. |
| Driver | A person who drives dispatched stock, `dbo.DriverMaster`, per company; licence number unique. Managed in Setting → Driver Master. |
| Active / Inactive / Deleted | Row state: `IsActive=1, IsDeleted=0` active; `IsActive=0, IsDeleted=0` inactive (hidden from other screens, shown on its own setup screen); `IsDeleted=1` deleted. |
| Requisition | A request from a location for stock. |
| POS Outlet | A location that runs point of sale. |
| <!-- TODO --> | Steward, roster, recipe, stock group, wastage, PO receive, plan, menu access — fill from the project-overview interview. |

---

## What is not in scope (as of this document)

<!-- TODO: list the boundaries. Explicit exclusions stop scope creep. -->
- Customer storefront (cart, checkout, customer profile) — not covered by specs or test cases for now (decided 2026-10-28).

---

## Resolved platform decisions

These are settled. Do not reopen without a formal ADR. The same table appears in `CLAUDE.md`; keep them in sync.

| Decision | Resolution |
|---|---|
| Backend stack | .NET 6 ASP.NET Core Web API (`RMS_API`, solution `BypassDhabaAPI.sln`) |
| Databases | SQL Server: Central DB + Tenant DB resolved from the `Origin` host; tenant rows filtered by `CompanyId` |
| Data access | Stored procedures via `FromSqlRaw` |
| Authentication | JWT bearer; identity and company from the token |
| API shape | `POST /{Controller}/{Method}`, `{ Status, Message, Data }`, business failures HTTP 200 |
| Client surface | Next.js 15 admin dashboard |
| Supported languages | <!-- TODO --> |
