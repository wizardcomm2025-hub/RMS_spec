# 02 — Add or edit a raw material

## Summary

A user with `create_raw_material` adds a raw material; with `edit_raw_material` edits one. One save stores the details, units, locations and vendors in a single transaction, for the token's company only.

## Main flow

1. "＋ Add Raw Material" opens the dialog (all active locations ticked by default); Edit loads the item with an edit token.
2. Fields: Name *, Alias, Reference No, SKU, Category * (Raw-type, searchable), Brand / Sub-brand (searchable), Parent, Main unit *, Alt unit *, Conversion * (> 0), Denominator, Cost, Price, Reorder + unit, Min order + unit, Shelf life (days), Yield %, Used by * (locations, searchable multi-select), Description, Remarks; Vendors tab (add, remove, default).
3. Save: the server checks permission, the fields (Q5), the name (Q3), that category / brand / sub-brand / locations / vendors are the company's and active, the unit lock (Q2) and the edit token; then saves everything at once (UTC). New locations get a zero opening stock row for **this** item.

## Alternative flows

- "Raw material name already exists (Raw Material)" (the type in brackets is the one that already has the name).
- "Category not found" · "Brand not found" · "Sub-brand not found" · "One or more locations are not available" · "One or more vendors are not available" · "The main unit can't change because this raw material is already in use.".
- "Raw material not found" · "This raw material was changed by someone else. Reload and try again." · "You don't have permission to perform this action." · field messages ("Name is required", "Conversion must be greater than 0", …).

## Acceptance criteria

1. Cost 12.50 is saved and shown as 12.50 (D3).
2. Making vendor V the default for item A doesn't change V's default on item B (D4).
3. Adding a location on edit creates a zero stock row for this item only (D5).
4. Removing a vendor and saving removes it (D6); Reference No, SKU and Remarks are saved (D7).
5. Another company's item id, category, brand, location or vendor → refused, nothing saved (D1, D8).
6. Main unit change on a used item → unit-lock message; on an unused item → saved.
7. Status isn't changed by the save; body `CompanyId`, `InventoryTypeId`, `ClassificationId` ignored.
8. The PO OCR dialog still creates a raw material with the prefilled values.

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
