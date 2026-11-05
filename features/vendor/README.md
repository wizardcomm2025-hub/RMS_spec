# Purchase → Vendor

A **vendor** (supplier) is a business the company buys from. Stored as `dbo.Distributor` (per company) with one primary contact in `dbo.DistributorContact` (address, phone, email, WhatsApp). Vendors are linked to raw materials (`DistributorStockItemMapping`), used on purchase orders (`PurchaseOrderMaster.VendorId`), PO receives (`ReceiveHead.VendorId`), barcodes and asset disposal. Saving a vendor also sends it to **Tally**.

Status (2026-11-02): **Built** — approved 2026-11-02; DBSCRIPT files not yet run on dev; manual tests deferred. Plan: [`RMS_API/specs/vendor/plan.md`](../../../RMS_API/specs/vendor/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `VND`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search.md`](./01-view-and-search.md) | See and search vendors; filter by status |
| 02 | [`02-add-or-edit.md`](./02-add-or-edit.md) | Add or edit a vendor and its contact |
| 03 | [`03-switch-on-off.md`](./03-switch-on-off.md) | Deactivate / reactivate a vendor |
| 04 | [`04-delete-unused.md`](./04-delete-unused.md) | Delete a vendor that has never been used |

## Defects this feature fixes (found 2026-11-02)

| # | Defect | Fixed by |
|---|---|---|
| D1 | **Cross-company contact attach:** for another company's vendor id the update changes nothing but `SP_AddEditVendor` still returns the id, and the contact is then inserted / updated against it | [02](./02-add-or-edit.md) |
| D2 | **Cross-company contact overwrite:** `SP_AddEditDistributorContact` updates `WHERE DistributorContactId = @id` only, with the id from the request | [02](./02-add-or-edit.md) |
| D3 | `CreatedBy` / `UpdatedBy` taken from the request body | [02](./02-add-or-edit.md) |
| D4 | **Duplicates allowed:** the save never checks name or GST (a separate check endpoint exists, can test only one of the two, and the save ignores it) | [02](./02-add-or-edit.md) |
| D5 | A vendor with more than one contact row appears **twice** in the list (unfiltered join) | [01](./01-view-and-search.md) |
| D6 | **Delete has no in-use check:** a vendor on POs, PO receives, raw-material links, barcodes or asset disposal can be deleted | [04](./04-delete-unused.md) |
| D7 | A **Tally** failure makes the screen report an error although the vendor was saved | [02](./02-add-or-edit.md) |
| D8 | Form rules unfit for real suppliers: name letters-only 35 chars ("A1 Traders & Co." refused); GST required but format unchecked; WhatsApp / email / address required | [02](./02-add-or-edit.md) |
| D9 | No server permission checks (`create_vendor`, `edit_vendor`, `delete_vendor`); SuperAdmin tokens pass `[Authorize]`; `GETDATE()`; no transaction (vendor saved, contact failed); no stale-save check; raw error text returned | all |
| D10 | Status changed by re-sending the whole form; list and page not on the recipe | [03](./03-switch-on-off.md), [`ui-ux.md`](./ui-ux.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | Active / Inactive **switch** + **Delete only if never used** (PO, PO receive, raw-material link, barcode, asset disposal) — disabled with the reason otherwise | 2026-11-02 |
| Q2 | **GSTIN optional**; when given it must be a valid 15-character GSTIN (`^[0-9]{2}[A-Z]{5}[0-9]{4}[A-Z][1-9A-Z]Z[0-9A-Z]$`, upper-cased) and unique in the company | 2026-11-02 |
| Q3 | **Required: name + phone** (10 digits). Optional but checked when given: email, WhatsApp (10 digits), website, address line 1 / 2, city, state, PIN (6 digits) | 2026-11-02 |
| Q4 | **Tally**: the vendor is saved first; if Tally fails the save still succeeds and the screen shows "Vendor saved. Couldn't send it to Tally — it will need to be added there by hand." (failure logged) | 2026-11-02 |
| Q5 | Name: up to 100 characters, master character set, unique in the company (not deleted). List loads every vendor; search and filter in the browser (vendor lists are small) | 2026-11-02 (default) |

## Depends on

Raw Material (vendor links), Purchase Order, PO Receive, Tally integration.
