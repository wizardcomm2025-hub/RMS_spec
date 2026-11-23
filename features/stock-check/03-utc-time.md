# 03 — Count times saved in UTC, shown in IST (pilot, 2026-11-18)

**Why:** count times show the database server's clock (`GETDATE()`), which the user reports as UTC, so a count made at 3:30 PM IST shows as 10:00 AM. The platform rule from now on: **save with `GETUTCDATE()`, show in IST (Asia/Kolkata, UTC+05:30, no daylight saving).** Stock Check is the pilot; other modules follow later.

## Flow
1. The user saves counts (all rows or one row) as today. Nothing changes on screen while saving.
2. `Sp_SaveStockCounts` stamps each new `StockCheck` row with the UTC time (`StockCheckDate`, `CreatedAt`, `StockCheckTime`).
3. The Stock Check list's **Last count** and the Stock Check Report's **Counted at** column (and its CSV) convert that UTC time to IST before formatting it in the company date / time format.
4. "Today" and date filters use the **IST calendar day**. The report's From–To and the dashboard's "locations not counted today" check cover 00:00–23:59 IST, which the SQL turns into a UTC range.

## Rules
| # | Rule |
|---|---|
| R1 | New `StockCheck` rows: `StockCheckDate` and `CreatedAt` = `GETUTCDATE()`; `StockCheckTime` = its time part (UTC). |
| R2 | The stock ledger day of a count (`@MoveDate`) is the **IST date** of the save. |
| R3 | `Stock.CreatedAt` / `UpdatedAt` written by the save stay on the server clock (other modules read them; out of the pilot). |
| R4 | IST day → UTC range: `[day 00:00 IST, next day 00:00 IST)` = `[DATEADD(MINUTE, -330, day), DATEADD(MINUTE, -330, day + 1))`. |
| R5 | The UI treats a `StockCheck` time from the API as UTC (no offset is sent) and shows it in IST, whatever time zone the browser is in. |
| R6 | Rows saved before the change are **not converted** (Q6). If the DB server clock is UTC they are already right; if it is IST they show 5 h 30 min later than they happened. |

## Acceptance criteria
1. Save a count at a known IST time: the list shows that IST date and time under Last count.
2. The Stock Check Report shows the same IST date and time, the CSV has the same values, and the count appears when From–To is today (IST).
3. A count saved between 00:00 and 05:30 IST appears under that IST date in the report, not the day before.
4. The dashboard's "locations not counted today" stops listing a location once it has a count today (IST).
5. With the browser set to another time zone (e.g. UTC), the times still show in IST.
