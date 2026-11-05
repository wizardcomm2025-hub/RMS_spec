# UI / UX — Purchase Order

> §12 recipe with **server paging**; badges centred, numbers and Actions right.

- List: `PageHeader` "Purchase › Purchase Order", **＋ New Purchase Order** (`create_purchase_order`); filters Date range · Status · Vendor · Search; columns PO Number · Date · Vendor · Items · Total · Status · Actions by status — Draft: **View · Edit · Delete**; Submitted: **View · Cancel**; Cancelled: **View** (icon-only, tooltips, right-aligned). Actions the status rules out are hidden; an allowed action blocked right now is greyed out with the reason (Cancel after goods are received: "Goods received against this PO — can't be cancelled."; no permission: the standard message) (2026-11-02); footer with total and paging; phone cards.
- Form page — see **Form page (rework 2026-11-02)** below.
- Cancel dialog with a required reason; Delete confirm (use cases 03, 04). Status chips: Draft (default), Submitted (info), Cancelled (error).

## Form page (rework 2026-11-02 — built)

Full page (a PO is a document with lines, too big for a dialog). Route unchanged: `/admin/purchase-order/new[?poId=]`.

```
PageHeader   Purchase › Purchase Orders (link) › New
             [←] New purchase order   Draft chip
┌ one card ─────────────────────────────────────────────────────────┐
│ ORDER DETAILS                                                     │
│ Vendor * [SearchableSelect]   PO date * [date]   PO number (read-only) │
│ ───────────────────────────────────────────────────────────────── │
│ ITEMS · n                                            [＋ Add item] │
│ #  Item *  Description  Qty *  UOM  Unit cost *  Amount  [🗑]      │
└───────────────────────────────────────────────────────────────────┘
Sticky footer bar:  Total 12,345.00          [Cancel] [Save draft] [Submit purchase order]
```
Compact layout (2026-11-02): one card with overline section titles (as in the Location dialog), no numbered step headers or subtitles; Add item sits on the Items title row; fixed column widths (# 48 · Qty 120 · UOM 80 · Unit cost 140 · Amount 120 · remove 56, Item and Description share the rest); all header fields 40 px high; a new PO opens with one empty line (dropped on save if left empty).

| # | Rule |
|---|---|
| F1 | **Back to list**: breadcrumb "Purchase Orders" links to the list; ← icon and **Cancel** go to the list. Dirty form → "Discard changes?" (same dialog as Location). Browser tab close while dirty → browser's leave-page prompt. |
| F2 | Title "New purchase order" or "Purchase order PO/…"; status chip beside it once saved. Loading keeps the header and shows a skeleton in the cards (no full-page spinner). |
| F3 | **Vendor** = `SearchableSelect` (active vendors + the PO's current one), full width on phones. |
| F4 | **Items toolbar** lives on the Items card, not next to the vendor: **＋ Add item** (primary, adds a row and focuses its Item field), **From requisition** (opens the requisition drawer — *hidden for now, 2026-11-02; requisition stays optional*), **Scan invoice** (opens the OCR dialog — *hidden for now, 2026-11-02, until tested end to end*). On phones the three become a row of full-width buttons. |
| F5 | Columns: # · Item * (search from the first letter, 300 ms pause, must be picked from the list) · Description · Qty * · UOM · Unit cost * · Amount (qty × cost, read-only) · remove (tooltip "Remove item"). Requisition lines show a small "REQ-…" caption under the description. Lines picked from a requisition still need an Item. |
| F6 | Validation inline per row after Save/Submit is pressed (red field + helper "Pick an item from the list", "Enter a quantity above 0", "Enter a cost of 0 or more"); plus one toast "Fix the highlighted items". Vendor / date errors under their fields. Empty rows (no item, no description) are dropped silently. |
| F7 | **Sticky footer**: Total (2 decimals, live) · **Cancel** · **Save draft** · **Submit purchase order**; spinners on the pressed button, all disabled while saving. |
| F8 | **Submit confirm**: "Submit purchase order to <vendor>? It can't be edited after this." Cancel / Submit purchase order. |
| F9 | Read-only (submitted / cancelled): info alert, fields shown as text (not greyed inputs), Items toolbar and remove icons hidden, footer shows only **Back to list**. |
| F10 | Theme tokens only (remove the `#fff` overrides in the form, view modal and requisition drawer). 360 px: cards stack, lines table scrolls sideways inside its card, footer wraps Total above the buttons. |

## View popup (2026-11-02)
Location-dialog style: dark title bar "Purchase order <number>" with Close (X); summary strip Vendor · PO date (company date format) · Status chip · Total (thousands separator); overline "ITEMS · n"; standard table # · Item · Description · Qty · UOM · Unit cost · Amount; skeleton while loading; "We couldn't load this purchase order. Close and try again." on failure; outlined **Close** button; full screen below 600 px.

## List date filter (fix 2026-11-02)
The date range picker edits a draft; the list reloads only on **Apply**. Picking a single day and Apply filters that day; Clear + Apply shows all dates. (Before the fix the first click was ignored, so the start date could never change.)

## Dates (2026-11-02)
Every PO date — list rows, phone cards, the form's date picker and read-only date, the view popup — uses the **company's date format** (`useDatePicker().userDateFormat` / `dateFormat`, set per company), the same as the list's date filter. The fixed `dd/MMM/yyyy` helper (`formatDate`) is no longer used for display on these screens; it is still used for the save payload, which the API already parses.
