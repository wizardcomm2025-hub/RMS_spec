# 03 — Stock ledger (system behaviour)

## Summary
Every change to a stock row writes one ledger row, so stock on any day since the ledger start can be worked out and every change can be traced.

## Main flow
1. A procedure that changes stock first sets `SESSION_CONTEXT` keys: `StockMoveType` (Purchase / TransferIn / TransferOut / Production / Wastage / Count / Sale), `StockMoveRef` (document number or id), `StockMoveDate` (business date), then clears them after its changes.
2. Trigger `trg_Stock_Ledger` (after insert / update / delete on `dbo.Stock`) writes, per changed row: company, location, item, business date (context or today), recorded at (now), quantity change in the main unit (new active qty − old active qty, alternate unit ÷ conversion), type (context or *Other*), document (context), user (row's UpdatedBy / CreatedBy).
3. Zero changes are not written. Multi-row statements write one row per changed stock row.

## Seeding
`seed_stock_ledger.sql` (run once, right after the trigger, when nobody is saving stock): one *Opening* row per item + location with the current stock (main unit), dated today. Re-running adds nothing for a company that already has Opening rows.

## Acceptance criteria
1. For every item + location, Σ ledger quantity = current stock (main unit) — the pre-check / health query shows 0 differences.
2. Old screens and POS changes are recorded too (*Other* / *Sale*).
3. A failed save writes no ledger rows (same transaction).

## Open questions
None.
