# API contract — Menu Section

Five existing endpoints (routes kept) and one new one. All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`. Shared responses: [`project-overview.md` → API conventions](../../project-overview.md#api-conventions).

## Endpoints

| Endpoint | Stored procedure | Purpose | Change |
|---|---|---|---|
| `POST /Setting/GetMenuSectionWithPagination` | `SP_GetMenuSectionWithPagination` | List (this page) | Full list, sorted; paging / search ignored; `updatedAt` |
| `POST /Setting/GetMenuSectionById` | `SP_GetMenuSectionById` | Load one | Company-scoped (was any company); inactive too; "Menu section not found" |
| `POST /Setting/AddEditMenuSection` | `SP_AddEditMenuSection` | Add (`0`) or rename | Permissions, validation, messages, stale check, company-scoped edit |
| `POST /Setting/ChangeMenuSectionStatus` | `SP_ChangeMenuSectionStatus` | Activate / deactivate | **New** |
| `POST /Users/ValidateMenuName` | `SP_ValidateMenuName` | Live name check | Trimmed, includes inactive |
| `POST /Setting/GetMenuSection` | `SP_GetMenuSection` | Active list (Recipe) | `[CompanyAuthorize]`; otherwise unchanged |
| `POST /Setting/DeleteMenuSection` | none | Legacy delete | **Blocked** |

## Request / response shapes

### List sections

**Request.** `{}` (old paging fields accepted and ignored). **Response.** `Data`: array of `menuSectionId`, `menuSectionName`, `isActive`, `meta_MenuSectionId`, `updatedAt` — active first, then by name.

### Get section by id

**Request.** `{ "menuSectionId": <int> }`. **Response.** `Data` with the same fields; `updatedAt` is the edit token (never null), sent back unchanged.

### Save section

| Field | Rule |
|---|---|
| `menuSectionId` | `0` = add; otherwise a section of the token's company |
| `menuSectionName` | Required after trim; ≤ 100; letters, numbers, spaces and & - ( ) ' / . ,; unique per company |
| `updatedAt` | **Edit only, required** |
| `isActive`, `meta_MenuSectionId`, `companyId` | Ignored |

**Response.** `Status=true`, `Data.menuSectionId`; failures `Status=false` with a message below.

### Change status

**Request.** `{ "menuSectionId": <int>, "isActive": <bool> }`. **Response.** `Status=true` (also when already in that state).

### Validate name

**Request.** `{ "menuName": "<string>", "menuSectionId": <int> }`. **Response.** `Data.rtrValue = true` when used by another section.

## Messages

| Endpoint | HTTP | `status` | Message (exact) | When |
|---|---|---|---|---|
| Save | 200 | false | `Menu section name is required` | Missing / empty |
| Save | 200 | false | `Menu section name must be 100 characters or less` | > 100 |
| Save | 200 | false | `Menu section name can only contain letters, numbers, spaces and & - ( ) ' / . ,` | Other characters |
| Save | 200 | false | `Menu section name already exists` | `Exist` |
| Save, Get by id, Status | 200 | false | `Menu section not found` | `NotFound` |
| Save (edit) | 200 | false | `This menu section was changed by someone else. Reload and try again.` | `Stale` |
| Status | 200 | false | `This menu section is used by active recipes. Move them to another section first.` | `HasActiveRecipes` |
| Save, Status | 200 | false | `You don't have permission to perform this action.` | No `create_menu_section` (add) / `edit_menu_section` (rename, status) |
| Delete | 200 | false | `Menu sections cannot be deleted. Deactivate the menu section instead.` | Always |
| Any | 200 | false | `Something went wrong. Please try again.` | Unexpected error |
| Any | 401 | — | `{ "message": "Unauthorized" }` | No / invalid token, SuperAdmin token |

Success messages (UI): `Menu section created`, `Menu section updated`, `Menu section activated`, `Menu section deactivated`.
