# 02 — Add or edit a recipe

## Summary

A user with `create_recipe` adds a recipe; with `edit_recipe` edits one. One save stores details, ingredients and locations in a single transaction, for the token's company only.

## Main flow

1. "＋ Add Recipe" → pick the type (Semi-Finished / Finished), then the fields as today: name, alias, SKU, reference no, category / sub-category, batch quantity + unit, yield %, shelf life, (Finished) menu section, POS unit, conversion, price, veg, image, POS item; description, remarks, used-by locations; ingredient lines (search raw materials and semi-finished recipes, quantity, unit, used / wastage).
2. Save: the server checks permission, fields (Q5), name, category, menu section, locations, every ingredient (company's, active or already on the recipe, not finished, not the recipe itself, no loop — Q3), the unit lock and the edit token; then saves all at once: changed lines updated, new lines added, removed lines switched off; locations added / removed **without touching stock rows of other locations**.
3. Cost: see [03](./03-recipe-cost.md).

## Alternative flows

- "Item name already exists (Raw Material)" · "Category not found" · "Menu section not found" · "One or more locations are not available" · "One or more ingredients are not available" · "Finished items can't be used as ingredients" · "A recipe can't use itself as an ingredient" · "This would make a loop: Onion Gravy already uses this recipe" · "Add at least one ingredient" · "The unit can't change because this recipe is already in use.".
- "Recipe not found" · "This recipe was changed by someone else. Reload and try again." · "You don't have permission to perform this action." · field messages.

## Acceptance criteria

1. Price 149.50 and conversion 0.5 are saved exactly (D2).
2. A failure during save leaves the old ingredients intact (D3).
3. Removing location L2 doesn't hide L2's stock rows of other items and keeps this item's non-zero stock visible in stock screens (D4).
4. Adding the recipe itself, or a recipe that already uses it, is refused with the loop message (D5).
5. Another company's ingredient / category / location, or a finished item as ingredient → refused (D6).
6. Editing another company's recipe id → "Recipe not found"; status and company unchanged by the save.
8. In edit mode the type cards are read-only; a direct API call that changes the type → "Recipe type can't be changed. Create a new recipe of the other type instead." (Q6).
7. The ingredient search never shows the recipe being edited, inactive or deleted items, or duplicates (D11).

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB and integrations not touched.

## Open questions

None.
