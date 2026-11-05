# 02 — Record a production

## Summary
A user with `create_product` records that a quantity of a recipe was made at one of their locations. Ingredient stock goes down by what was actually used, the produced item's stock goes up, and the server works out the cost.

## Main flow
1. **New production** → page `/admin/production/add`. Fields: Item * (searchable; producible items only, D1) · Location * (my locations) · Date * (today) · Quantity * (in the item's unit; batch shown as a hint, e.g. "Recipe makes 5 kg").
2. Picking item + location loads the recipe: per ingredient Ingredient · Unit (main) · In stock here · Standard (scaled: line × quantity ÷ (batch × yield)) · **Actual** (prefilled with Standard, editable) · Variance (Actual − Standard) · Cost (preview: Actual × unit cost) · **Adjustment ₹** · **Reason** (required when the adjustment isn't 0). Changing the quantity rescales Standard and resets untouched Actuals.
3. Overhead ₹ (≥ 0). Sticky footer: Ingredients ₹ · Adjustments ₹ · Overhead ₹ · **Final ₹** · Unit cost ₹/unit · Cancel · **Save production**.
4. Confirm "Record 10 kg of Gravy at Central Kitchen? Ingredients go out of stock now and the production can't be changed later." → server checks permission, item (company, producible), location (user's), date, quantity > 0, overhead ≥ 0, every recipe ingredient present once and nothing else, Actual ≥ 0, reason when adjusted, then in one transaction under the location's stock lock: stock check per ingredient (main unit), production + lines saved with standard / actual / cost, ingredients taken, produced quantity added (row created if missing) → "Production of 10 kg Gravy saved" → view page.

## Alternative flows
"Select an item that has a recipe" · "Select one of your locations" · "Production date can't be in the future" · "Quantity must be greater than 0" · "Overhead can't be negative" · "Actual quantity can't be negative" · "Enter a reason for the adjustment on Onion" · "Recipe has changed. Reload the ingredients." (missing / extra ingredient) · "Not enough Onion at Central Kitchen: 2 kg in stock, 3 kg needed" · "Final cost can't be negative" · "You don't have permission to perform this action." · "Something went wrong. Please try again."

## Acceptance criteria
1. Produced quantity always reaches stock, also the first time at a location (P1).
2. Stock goes down by Actual; a short ingredient blocks the save; nothing is floored to 0 (P2, P3).
3. Units converted to the main unit; parallel stock changes never lost (P4).
4. Costs computed on the server from Actual and the item cost; browser totals ignored (P5).
5. Only the company's recipe items, the user's locations and the recipe's ingredients (P6); the date saved is the date picked (P7); no edit path (P8).

## Open questions
None.
