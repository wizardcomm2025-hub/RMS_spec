# API contract — Vendor

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]`.

| Endpoint | Permission | Change |
|---|---|---|
| `/Setting/GetVendorList` | — | One row per vendor; + `canDelete`, `updatedAt` |
| `/Setting/GetVendorById` | — | + `updatedAt`; "Vendor not found" |
| `/Setting/AddEditVendor` | `create_vendor` / `edit_vendor` | Validation (Q2, Q3, Q5), `Sp_SaveVendor`, codes → messages, `status: false` on refusal; Tally after a successful save (Q4); request + `updatedAt` |
| `/Setting/ChangeVendorStatus` (**new**) | `edit_vendor` | `{ distributorId, isActive }` |
| `/Setting/DeleteVendor` | `delete_vendor` | Delete-if-unused |
| `/Setting/DuplicateCheckVendorNameOrGST` | — | `[CompanyAuthorize]` (live check in the dialog) |

Messages (`status: false`): `Vendor name already exists` · `GST number already exists for {name}` · `Enter a valid 15-character GSTIN, e.g. 27ABCDE1234F1Z5` · `This vendor is in use and can't be deleted. Deactivate it instead.` · `Vendor not found` · `This vendor was changed by someone else. Reload and try again.` · `You don't have permission to perform this action.` · `Something went wrong. Please try again.` · field messages. 401 for no / invalid / SuperAdmin token.

Success: "Vendor created", "Vendor updated", "Vendor saved. Couldn't send it to Tally — it will need to be added there by hand." (success with warning), "Vendor activated", "Vendor deactivated", "Vendor deleted".
