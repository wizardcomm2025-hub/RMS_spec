# 05 — Show the last purchase cost

Status (2026-10-30): **Built** — approved 2026-10-30; list procedure not yet run on dev.

Note: the dialog had no Cost input at all (the value was only carried through on save), so **Standard cost** was added as a new field in the Stock control section.

## Summary

The list's Cost column shows what the company **last paid** for the raw material (pre-tax, per main unit), taken from the latest PO receive. Before the first purchase it shows the cost typed in the dialog, renamed **Standard cost**. Nothing is stored twice: PO receive already writes `Item_Price_History`.

## Decisions

| # | Decision | Date |
|---|---|---|
| C1 | List cost = **last purchase cost**, falling back to the standard cost | 2026-10-30 |
| C2 | **Pre-tax** (`Item_Price_History.priceWithoutTax`) — input GST is recovered by registered restaurants. A per-company "cost includes tax" setting is a possible later change (composition-scheme companies) | 2026-10-30 |
| C3 | **Per main unit.** A price recorded in the alternate unit is converted: price per main unit = price per alt unit × `Conversion` (alt units in 1 main unit). A price in any other unit isn't used | 2026-10-30 |
| C4 | Latest receive **across all locations** (by `AsOnDate`, then `CreatedAt`); per-location cost belongs to stock reports | 2026-10-30 |
| C5 | Weighted average cost is **not** shown here; it belongs to stock-valuation and food-cost reports | 2026-10-30 |

## Main flow

1. The list procedure returns, per row: `LastCost`, `LastCostDate` (null when never purchased) and `StandardCost` (`StockItem.PurchaseCost`).
2. Cost column:

| Situation | Shows | Tooltip |
|---|---|---|
| Purchased | `₹42.00` | "Last purchase, excl. tax · 28 Oct 2026" |
| Never purchased, standard cost > 0 | `₹40.00` in grey | "Standard cost (no purchase yet)" |
| Neither | "—" | — |

3. Dialog: the **Cost** field is relabelled **Standard cost**, helper "Used until the first purchase. The list then shows the last purchase cost." The preview card's "Last cost / KG" shows the last purchase cost when there is one.
4. Phone card: "Cost ₹42.00" with the same rules (no tooltip; "(std)" after a standard cost).

## Acceptance criteria

1. Item received at ₹45 then ₹42 (pre-tax, main unit) → list shows ₹42.00 with the later date.
2. Received in grams at ₹0.042 / g, conversion 1000 → list shows ₹42.00 / Kg.
3. Never received, standard cost 40 → grey ₹40.00, "Standard cost (no purchase yet)"; standard 0 → "—".
4. Tax on the receive doesn't change the shown cost.
5. Another company's history never affects the row (`Item_Price_History.CompanyId`).
6. Saving the dialog doesn't change the last purchase cost; a new PO receive does, on the next list load.

## Platform notes

- DB: `SP_GetItemListWithPagination` adds `LastCost`, `LastCostDate`, `StandardCost` (an `OUTER APPLY` on `Item_Price_History`, `IsActive = 1`, same company). `PurchaseCost` stays for older callers. No table change. Optional index `Item_Price_History (ItemId, CompanyId, AsOnDate DESC)` if the list gets slow — not part of this change.
- API: `ResRawMaterialListRow` gains the three fields.
- UI: cost cell and card text, dialog label and helper, preview card.

## Test cases

| ID | Steps | Expected |
|---|---|---|
| `RAW-05-T01` | Receive RM-A on two POs (₹45, then ₹42 pre-tax); open the list. | ₹42.00; tooltip shows the later date and "excl. tax". |
| `RAW-05-T02` | New RM-B with Standard cost 40, never received. | Grey ₹40.00, tooltip "Standard cost (no purchase yet)". |
| `RAW-05-T03` | Receive RM-C in its alternate unit (g) at ₹0.042, conversion 1000. | ₹42.00. |
| `RAW-05-D01` | `GetItemListPagination` for CO-A while CO-B has history for an item id of CO-A (bad data). | Only CO-A history used. |
