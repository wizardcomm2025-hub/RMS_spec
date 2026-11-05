# UI / UX — PO Receive

> §12 list recipe with server paging; form page like Purchase Order (one card, overline sections, sticky footer). Theme tokens only.

## List
`PageHeader` "Purchase › PO Receive", **＋ Receive goods** (`receive_receive_asset`), About links to Purchase Order. Filters: date range (Apply) · Status · Vendor (`SearchableSelect`) · Location (`SearchableSelect`) · search. Columns: Receive No · Date · PO No · Vendor · Location · Invoice / Challan (two lines) · Amount · Status (centred chip: Draft default, Posted success) · Actions (right; Draft: View · Edit · Delete; Posted: View). Footer "1–25 of 40 receipts" + page size. Phone cards. Dates in the company format.

## Form (`/admin/purchase-order-receive/new[?receiveHeadId=]`)
```
PageHeader  Purchase › PO Receive (link) › New      [←] Receive goods   Draft chip
┌ one card ───────────────────────────────────────────────────────────────┐
│ RECEIPT                                                                  │
│ Purchase order * [SearchableSelect]   Vendor (text)   Location * [select] │
│ Received date *   Invoice no   Challan no   [Invoice file] [Challan file]  │
│ ───────────────────────────────────────────────────────────────────────  │
│ ITEMS · n                                                                │
│ # Item  Ordered  Received before  Remaining  Receiving*  Damaged  UOM  Unit cost  Tax  Amount │
└──────────────────────────────────────────────────────────────────────────┘
Sticky footer: Lines 12,000.00 · Discount [ ] · Receive total [12,000.00]   [Cancel] [Save draft] [Post receipt]
```
| # | Rule |
|---|---|
| F1 | Back arrow, linked breadcrumb, Cancel → list; dirty → "Discard changes?"; tab close → browser prompt |
| F2 | PO picker lists receivable POs only; locked after the first save. Picking a PO loads its lines with Receiving = Remaining |
| F3 | Fully received lines: greyed, Receiving shows "Fully received". Inline errors under the field (over-receipt "Only 4 kg left", damaged > receiving) |
| F4 | Receive total prefilled from lines − discount; editable; when different: caption "Differs from lines by 120.00" (no block) |
| F5 | Files: pdf / jpg / png ≤ 5 MB with name + remove; saved files open as links |
| F6 | Post asks to confirm; spinners on the pressed button; all disabled while saving |
| F7 | Posted receive opens read-only (text, no inputs), footer "Back to list" |
| F8 | 360 px: fields stack, table scrolls inside the card, footer wraps total above buttons |

## View popup
Same as the PO view popup: dark title "Receipt GRN/…", summary (PO, Vendor, Location, Date, Invoice / Challan, Status, Total), items table, attachment links, outlined Close.

## PO list (use case 04)
Submitted PO chip with caption "Partly received" / "Fully received" under it.
