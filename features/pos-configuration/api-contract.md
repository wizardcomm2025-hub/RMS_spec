# API contract — POS Configuration

Tax Setting: all `POST /Setting/...`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetTaxSettingList` | `viewlist_tax_setting` | **Reshaped**: `{ status: "All" \| "Active" \| "Inactive", search }` → rows `taxSettingId, taxSettingName, isActive, isDefault, componentCount, componentsText, totalTaxPercentage, updatedAt` |
| `/Setting/GetTaxSettingDetail` | `viewlist_tax_setting` | `{ taxSettingId }` → `{ taxSetting: { taxSettingId, taxSettingName, isActive, isDefault }, taxComponentList: [{ taxComponentId, componentName, currentPercentage }] }` (active components only); another company's id → `Status false`, "This tax group was not found" |
| `/Setting/AddEditTaxSetting` | `create_tax_setting` (id 0) / `edit_tax_setting` | `{ taxSettingId, taxSettingName, taxComponentList: [{ taxComponentId, componentName, currentPercentage }] }` → `{ taxSettingId }`; component `isActive` is ignored (sent = active, missing = deactivated) |
| `/Setting/SetTaxSettingStatus` (**new**) | `edit_tax_setting` | `{ taxSettingId, isActive }` |
| `/Setting/SetDefaultTaxSetting` (**new**) | `edit_tax_setting` | `{ taxSettingId }` |

Messages: "Group name is required" · "Use up to 50 characters" · "A tax group with this name already exists" · "Add at least one component" · "Component name is required" · "Each component needs a different name" · "Enter a percentage from 0 to 99.99" · "The total must be under 100%" · "This tax group was not found" · "The default tax group can't be deactivated. Make another group the default first." · "Activate this tax group first." · "You don't have permission to perform this action." · "Something went wrong. Please try again." 401 for no / invalid / SuperAdmin token.

Order Channels: unchanged PosPro endpoints (`/Api/Setting/GetOrderChannelList`, `GetOrderChannelTypes`, `GetOrderChannelById`, `AddEditOrderChannel`, `UpdateOrderChannelStatus` on `NEXT_PUBLIC_POS_URL`), same request shapes.
