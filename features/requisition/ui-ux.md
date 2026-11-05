# UI / UX — Requisition

> List on the §12 recipe; create / edit page on the Stock Check sheet pattern; view page like the PO view. Theme tokens only.

## List
`PageHeader` "Stock › Requisition", **＋ New requisition** (`create_`), About links (Inbound Requisition, Item Receive, Transfer Report). Filter bar: search (number) · date range (Apply) · Status · From · To (`SearchableSelect`). Columns: Requisition No (no wrap) · Date · From → To · Items · Created by · Decided by · Status (centred chip) · Transfers (🚚 in transit n / received n, click → transfer list dialog) · Actions (right: View; Pending → Edit · Cancel). Footer + paging; phone cards.

## Create / edit (`/admin/item-requisition/create[?reqId=]`)
```
PageHeader  Stock › Requisition (link) › New     [←] New requisition | Requisition REQ/…  Pending chip
┌ card ─────────────────────────────────────────────────────────────────┐
│ REQUISITION  Date * [date]   From * [▾]   To * [▾]   Number (read-only) │
└────────────────────────────────────────────────────────────────────────┘
Filter bar [🔍 Search item or group] [Show ▾]      Tabs  Raw Item | Semi-Finished | …
┌ table ─────────────────────────────────────────────────────────────────┐
│ Item (pinned) · Group · In stock at From · In transit · Request [  ][kg▾] │
└────────────────────────────────────────────────────────────────────────┘
Sticky footer: 4 items requested             [Cancel] [Submit] [Submit and approve]
```
Discard prompt on leave / location change; tab-close prompt; skeletons; existing lines of other tabs are kept when switching tabs (one requisition across types).

## View (`/admin/item-requisition/view?reqId=`)
Header with number + status chip; summary (date, from, to, created / decided by + note); items table; transfers list. Buttons by status and permission: Pending → Approve · Decline · Edit · Cancel; any → Duplicate · Export · Print. Confirm / note dialogs.
