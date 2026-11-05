# API contract — Recipe

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]` on every endpoint below.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetRecipeListPagination` | — | + `isActive`, `stockGroupId`, `menuSectionId` filters; rows + `isActive`, `canDelete`, `unitCost`, `isManualCost`, `totalCount` |
| `/Setting/GetStockItemByIdForRecipe` | — | Company-scoped; + `isActive`, `updatedAt`, `isUsed`, `isManualCost`, `calculatedCost`; lines + `unitCost`; "Recipe not found" |
| `/Setting/StockItemSuggestionForRecipe` | — | + `excludeId`; + `unitCost` |
| `/Setting/AddEditRecipeItem` | `create_recipe` / `edit_recipe` | Validation, codes → messages, `status: false` on refusal; request + `isManualCost`, `updatedAt` |
| `/Setting/ChangeRecipeStatus` (**new**) | `edit_recipe` | `{ stockItemId, isActive }` |
| `/Setting/DelRecipeStockItemById` | `delete_recipe` | Delete-if-unused |
| `/Setting/DelRecipe` | `edit_recipe` | Company-scoped |
| `/Setting/GetInventoryType`, `GetSubStockCategoryById`, `UploadRecipeImageNew` | — | `[CompanyAuthorize]` |

## Messages (HTTP 200, `status: false`)

`Item name already exists ({type})` · `Category not found` · `Menu section not found` · `One or more locations are not available` · `One or more ingredients are not available` · `Finished items can't be used as ingredients` · `A recipe can't use itself as an ingredient` · `This would make a loop: {name} already uses this recipe` · `Add at least one ingredient` · `The unit can't change because this recipe is already in use.` · `Recipe type can't be changed. Create a new recipe of the other type instead.` · `This recipe is in use and can't be deleted. Deactivate it instead.` · `Recipe not found` · `This recipe was changed by someone else. Reload and try again.` · `You don't have permission to perform this action.` · `Something went wrong. Please try again.` · field messages. 401 for no / invalid / SuperAdmin token.

Success: "Recipe created", "Recipe updated", "Recipe activated", "Recipe deactivated", "Recipe deleted".
