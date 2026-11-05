# 02 — Add or edit a vendor

## Summary

A user with `create_vendor` adds a vendor; with `edit_vendor` edits one. Vendor and contact are saved together in one transaction for the token's company, then sent to Tally (Q4).

## Main flow

1. "＋ Add Vendor" opens the dialog; Edit loads the vendor with an edit token.
2. Fields (Q3): Vendor name *, GSTIN, Phone *, WhatsApp, Email, Website, Address line 1 / 2, City, State, PIN; flags For inventory / For assets / Accepts disposal (kept as today).
3. Save: the server checks permission, fields, name and GSTIN uniqueness, company and edit token; saves vendor + primary contact atomically (UTC, user from the token); then sends the vendor to Tally.

## Alternative flows

- "Vendor name already exists" · "GST number already exists for <vendor>" · "Enter a valid 15-character GSTIN, e.g. 27ABCDE1234F1Z5" · "Vendor not found" · "This vendor was changed by someone else. Reload and try again." · "You don't have permission to perform this action." · field messages.
- Tally failure → success with "Vendor saved. Couldn't send it to Tally — it will need to be added there by hand.".

## Acceptance criteria

1. Another company's vendor id → "Vendor not found"; no contact row added or changed for it (D1).
2. Another company's contact id → ignored; the vendor's own primary contact is updated (D2).
3. Duplicate name / GSTIN → refused (D4); GSTIN optional, upper-cased, format-checked (Q2).
4. "A1 Traders & Co." accepted; WhatsApp, email, address optional (D8).
5. Body `CreatedBy`, `CompanyId`, `IsActive` ignored; status not changed by the save.
6. Tally down → vendor saved, warning shown (D7).

## Platform notes

See [`api-contract.md`](./api-contract.md), [`data-model.md`](./data-model.md), [`ui-ux.md`](./ui-ux.md). Central DB not touched; Tally per Q4.

## Open questions

None.
