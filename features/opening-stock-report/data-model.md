# Data model — Opening Stock Report (stock movement)

Tenant DB. Objects in `RMS_API/DBSCRIPT/`. **One new table + one trigger.**

## New table `dbo.StockLedger`
| Column | Type | Notes |
|---|---|---|
| `StockLedgerId` | bigint identity PK | |
| `CompanyId` | bigint NOT NULL | from the stock row, else the item |
| `WarehouseId` | bigint NOT NULL | |
| `StockItemId` | uniqueidentifier NOT NULL | |
| `MoveDate` | date NOT NULL | business date (`StockMoveDate` context, else today) |
| `RecordedAt` | datetime NOT NULL | `GETDATE()` |
| `Qty` | decimal(18,4) NOT NULL | signed change, item main unit |
| `MoveType` | nvarchar(30) NOT NULL | Opening / Purchase / TransferIn / TransferOut / Production / Wastage / Count / Sale / Other |
| `RefNumber` | nvarchar(100) NULL | document number / id |
| `UserId` | uniqueidentifier NULL | |
Index: `(CompanyId, WarehouseId, StockItemId, MoveDate)` include `Qty, MoveType`.

## Trigger `dbo.trg_Stock_Ledger` (new, `DBSCRIPT/Triggers/`)
After insert / update / delete on `dbo.Stock`; see [03](./03-stock-ledger.md).

## Procedures
| Object | Change |
|---|---|
| `SP/dbo.Sp_GetOpeningStockReport` (**rewritten**) | `@CompanyId, @UserId, @FromDate, @ToDate, @LocationId, @InventoryTypeId, @Show (All/Moved/Negative), @Search, @PageNumber, @PageSize (0 = all)` → item × location lines: opening, in, out, count adj., closing, unit cost, closing value; `TotalCount`, `OpeningValue`, `ClosingValue`, `LedgerStart` |
| `SP/dbo.Sp_GetStockMovements` (**new**) | `@CompanyId, @UserId, @StockItemId, @LocationId (NULL = all mine), @FromDate, @ToDate` → opening row + movements (date, location, type, ref, qty, balance after, by), max 500 |
| `SP/dbo.SP_SaveRMSPOReceive`, `Sp_DispatchRequisition`, `Sp_ReceiveTransfer`, `Sp_SaveProduction`, `Sp_SaveWastage`, `Sp_CancelWastage`, `Sp_SaveStockCounts`, `SP_AddEdit_Stock_POS` | Set / clear `SESSION_CONTEXT` (type, document, date) around their stock changes — no other change |

## Run-once script
`specs/opening-stock-report/seed_stock_ledger.sql` — Opening rows from current stock.
