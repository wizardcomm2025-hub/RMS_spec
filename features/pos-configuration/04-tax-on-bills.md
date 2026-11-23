# 04 — Tax on POS bills

## Summary
A POS bill charges only the default tax group's active components.

## Main flow
1. When a bill is created (`SP_CreateBill`), the components copied into `BillTaxComponent` and summed into the tax amount are the **default** group's active components only.
2. The bill records that group's id and name (`BillHead.TaxSettingId`, `TaxSettingName`).
3. `GetTaxDt` (the components the POS shows before billing) returns the same components.

## Alternative flows
No default group (or the default has no active component): no tax, as today when nothing is active.

## Acceptance criteria
1. With *GST 5%* (default) and *GST 18%* both active, a ₹1,000 bill charges ₹50 tax, not ₹230 (T1).
2. The bill's tax group name is the default group's.
3. `GetTaxDt` and the bill agree.
4. Changing the default changes only bills made afterwards (D5).

## Open questions
None.
