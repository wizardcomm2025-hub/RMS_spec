# API contract — User List

All `POST`, `{ Status, Message, Data }`, company and user from the token, `[CompanyAuthorize]` on every Users endpoint below except `ValidateUserName`, which gets `[Authorize]` (it had none; SuperAdmin company creation also calls it).

| Endpoint | Permission | Change |
|---|---|---|
| `/Users/GetUserList` | — | Server paging kept; new `isActive` filter; rows carry `roleNames`, `roleIds`, `hasSystemRole`, `updatedAt`; `roleList` adds `isSystemRole` |
| `/Users/GetUserDtlByUserId` | — | `updatedAt`; "User not found" |
| `/Users/AddEditUser` (also `AddNewUser`, `UpdateUser`) | `create_userlist` / `edit_userlist` | Same save path: server validation, `Sp_SaveUser` codes → messages, `status: false` on any refusal; `data.updatedAt` = new edit token |
| `/Users/AddEditAssignRole` | `edit_userlist` | Company, active-role, self and last-admin checks (D1) |
| `/Users/SetUserActive` | `edit_userlist` | Self and last-admin checks; codes |
| `/Users/DeleteUser` | — | **Refused**: "Users cannot be deleted. Deactivate the user instead." |
| `/Users/ChangeUserPassword` | — (own password) | No password in the response (D7) |
| `/Users/ValidateUserName` | — | `[Authorize]` |
| `/Users/GetDepartmentDDl`, `GetReportingHeadDDl`, `GetAltReportingHeadDDl` | — | `[CompanyAuthorize]` |
| `/Users/GetUserDetailsList` | — | Company from the token (was the body) |
| `/Users/ViewProfileDetails` | — | No password hash; `userDtl.updatedAt` edit token |

No response carries a password or password hash (D7, D10).

## Messages (HTTP 200, `status: false`)

`User name already exists` · `Phone number already exists` · `Department not found` · `Reporting head not found` · `One or more locations are not available` · `Pick at least one role` · `One or more roles are not available` · `You can't change your own roles or status.` · `This is the last active admin. Give another user the Admin role first.` · `User not found` · `This user was changed by someone else. Reload and try again.` · `Users cannot be deleted. Deactivate the user instead.` · `You don't have permission to perform this action.` · `Something went wrong. Please try again.` · field messages as the form. 401 for no / invalid / SuperAdmin token.

Success (UI): "User created", "User updated", "Roles saved", "User activated", "User deactivated".
