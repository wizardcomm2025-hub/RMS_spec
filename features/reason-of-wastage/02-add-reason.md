# 02 — Add a reason of wastage

## Summary

A company user with `create_reason_of_wastage` adds a reason by typing its text — on this page, or with the "＋" next to the reason dropdown in Stock → Wastage. The system checks the text (allowed characters, unique in the company including inactive reasons), saves it as Active for the caller's company, and shows it in the list (or selects it in the Wastage row).

## Prerequisites

- **Backend.** `AddEditResonMaster` with server validation, exact messages and `Status=false` on any refusal (see [`api-contract.md`](./api-contract.md#save-reason)); new `ValidateWastageReason` for the live check.
- **Data.** `SP_AddEditResonMaster` changed; new `SP_ValidateWastageReason`.
- **Depends on.** [03](./03-rename-reason.md) shares the save contract.

## Actors & preconditions

- **Actor:** a company user with `create_reason_of_wastage`.
- **Trigger:** "＋ Add Reason" in the header or empty state; or "＋" beside a Wastage Entry row's Reason dropdown.

## Main flow

1. The user clicks "＋ Add Reason"; the dialog "Add Reason" opens.
2. The user types the Reason; the system checks for duplicates as they type.
3. The user clicks "Add Reason"; the button shows a spinner and the dialog can't be closed.
4. The system trims the text, validates it and saves the reason for the token's company: `IsActive = 1`, `IsDelete = 0`, `UpdatedBy` = caller, `UpdatedOn` = now (UTC).
5. The dialog closes, "Reason added" appears and the list refreshes.

## Alternative flows

- **From Wastage Entry.** The same dialog opens over Wastage Entry. After saving, the toast "Reason added", the active list reloads and the new reason is **selected in the row** whose "＋" was clicked. The "＋" shows only with `create_reason_of_wastage` (and Wastage save access, as today) (D3).
- **Empty / spaces only.** "Reason is required".
- **Other characters.** "Reason can only contain letters, numbers, spaces and & - ( ) ' / . ,".
- **Duplicate.** The trimmed text equals (case-insensitive) another non-deleted reason of the company, active or inactive: "Reason already exists" under the field and as a toast; nothing saved (D4). A deleted reason with the same text doesn't block.
- **Same text in another company.** Allowed.
- **Long text.** No maximum; no counter. Very long text wraps.
- **No permission (direct call).** "You don't have permission to perform this action."
- **Double click.** One reason is created.
- **Discard.** Closing a changed form asks "Discard changes?".
- **Server failure.** "Something went wrong. Please try again." (or "Failed to save reason" when unreachable); the dialog stays open.

## Acceptance criteria

1. Given a valid text, then "Reason added" and the reason is listed as Active.
2. Given " expired " while "Expired" exists (active or inactive), then "Reason already exists" and nothing is saved.
3. Given "Expired" exists only as deleted (`IsDelete = 1`), when CO-A adds "Expired", then it is saved (D4).
4. Given "Spoiled" exists only in CO-B, when CO-A adds "Spoiled", then it is saved.
5. Given "Spill@Bar", then the characters message and nothing saved; given "Damaged (transit) - box/crate, 2", then it is saved.
6. Given a 500-character valid text, then it is saved and shown wrapped.
7. Given a duplicate from Wastage Entry, then the duplicate message, never "Reason added" (D3).
8. Given the body carries `IsActive = false`, `CompanyId` or `UpdatedBy`, then they are ignored: Active, token company and caller.
9. Given a user without `create_reason_of_wastage`, then the Add button and the Wastage Entry "＋" are hidden and a direct call returns the permission message.
10. Given a quick-add from Wastage Entry row 2, then the new reason is selected in row 2 only.

## Platform notes

### RMS API

- `POST /Setting/AddEditResonMaster` with `ReasonMasterId = 0` — `[CompanyAuthorize]`, `create_reason_of_wastage`. `POST /Setting/ValidateWastageReason` — `[CompanyAuthorize]`.

### Data — Tenant DB

- Inserts `WastageReasonMaster` under a per-company lock; `UpdatedOn = GETUTCDATE()`; `DefaultWastageReasonId` stays NULL.

### Data — Central DB

Not touched.

### Integrations

Not touched.

### Admin dashboard

- Dialog: [`ui-ux.md`](./ui-ux.md#3-dialog--form); Wastage Entry: [`ui-ux.md`](./ui-ux.md#11-wastage-entry).

## Configuration

None.

## Out of scope

- Bulk import; restoring a deleted reason.

## Open questions

None.
