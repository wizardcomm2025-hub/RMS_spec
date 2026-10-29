# Data model — {{FEATURE_NAME}}

<!-- TODO: one short paragraph describing the data this feature owns, in plain language. -->

> **Stores.** SQL Server: the **Tenant DB** (company data, shared by several companies, every row filtered by `CompanyId`) and the **Central DB** (control plane). Leave out the section for a store the feature doesn't touch, and say so in one line.
>
> **SQL source.** Scripts live in `RMS_API/ProductManagement.API/Database Scripts/` (`Stored Procedure/`, `Table/`, `Tenant/`, `CentralDB/`, `Function/`, `UserDefinedFunction/`) and must match this file. Scripts are re-runnable (`CREATE OR ALTER`, `IF NOT EXISTS`).
>
> **Naming.** Follow the existing schema. Read the tables you touch and match their style.

## Tenant DB

### Tables

<!-- TODO: each table this feature creates, alters or relies on. Change is one of: New · Altered (list only new/changed columns below) · Existing (read only, no change). -->

| Table | Change | Primary key | Purpose |
|---|---|---|---|
| `<TableName>` | Existing | `<Id>` | <what it stores> |

### Columns

<!-- TODO: one subsection per new or altered table, and the existing columns whose meaning the feature relies on. -->

#### `<TableName>`

| Column | Type | Null | Notes |
|---|---|---|---|
| `<Id>` | bigint identity | NOT NULL | PK |
| `CompanyId` | bigint | NOT NULL | Company boundary; every read and write filters on it |

<!-- Keys and constraints, one bullet each:
- **FK** `<column>` → `<Table>`
- **Unique** `(CompanyId, <column>) WHERE IsDeleted = 0`
- **Checked in the SP:** rules the database can't enforce (CompanyId match, cross-table consistency) -->

### Stored procedures

<!-- TODO: procedures this feature adds or changes, and existing ones it calls, one line each with its parameters and purpose. -->

| Procedure | Change | Parameters | Purpose |
|---|---|---|---|
| `<SP name>` | New / Altered / Existing | `@CompanyId, …` | <purpose> |

### Seed data

<!-- TODO: rows that must exist (permissions, menu rows, lookup values). Write "None." if nothing is seeded. -->

### Indexes

<!-- TODO: non-key indexes that matter, or "No extra indexes." -->

## Central DB

<!-- TODO: control-plane tables or procedures touched (companies, SiteRegistry, plans, menu access), or "Not touched." -->

## Data lifecycle

<!-- TODO: IsActive / IsDeleted meanings for this feature's rows, what other screens filter on, audit columns (CreatedBy/At, UpdatedBy/At). -->

| State | `IsActive` | `IsDeleted` | Meaning |
|---|:-:|:-:|---|
| Active | 1 | 0 | <meaning> |

## Migration notes

<!-- TODO: additive or breaking changes, backfills, pre-check queries, the deployment order of SQL scripts, API and UI. -->
