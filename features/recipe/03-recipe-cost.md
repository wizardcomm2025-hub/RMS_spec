# 03 — Recipe cost: calculated, with manual override

## Summary

The recipe's cost per 1 recipe unit is **calculated from its ingredients** (Q1). A user can switch on **Manual cost** and type a cost that replaces the calculation until switched off.

## Main flow

1. Ingredient cost per its main unit: a raw material uses its **last purchase cost** (pre-tax, per main unit — Raw Material use case 05), else its standard cost; a semi-finished recipe uses **its own recipe cost** (calculated or manual), worked out the same way down the chain.
2. Line cost = line quantity in the ingredient's main unit (`ReceipeDetail.PrimaryQty`) × ingredient cost.
3. Batch cost = sum of line costs. **Cost per recipe unit = batch cost ÷ (batch quantity × yield % ÷ 100)**; yield empty or 0 counts as 100 %.
4. Dialog: each ingredient line shows its unit cost and line cost; a Cost card shows batch cost, cost per unit and "Calculated". A **Manual cost** switch opens a field; when on, the card shows "Manual" and the calculated value under it for comparison. Live Costing (food cost %) uses the effective cost.
5. Save stores the effective cost in `StockItem.PurchaseCost` (so other screens keep working) and `IsManualCost`. The list always shows the live value: calculated now (ingredient prices may have moved) or the manual one.

## Alternative flows

- An ingredient with no cost at all counts as 0 and the line shows "No cost" (warning colour); the card says "Some ingredients have no cost".

## Acceptance criteria

1. Recipe: 2 Kg onion at ₹40 / Kg + 500 g oil at ₹150 / L (conversion 1000) → batch ₹155; batch 4 Kg at 80 % yield → ₹48.44 / Kg.
2. Semi-finished Gravy (₹48.44 / Kg) used as 250 g in Paneer Masala → line ₹12.11.
3. A new purchase of onion at ₹50 changes the list cost of both recipes on the next load (calculated ones only).
4. Manual cost 60 → list shows 60.00 with "Manual"; switching manual off returns to the calculated value.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
