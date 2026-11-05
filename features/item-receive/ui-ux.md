# UI / UX — Item Receive

Follows `UI_UX_STANDARDS.md` §12 (list) and the requisition sheet page pattern.

## List
`PageHeader` "Stock › Item Receive" (no description; About links: Inbound Requisition, Transfer Report). Filter bar in one row from `lg`: search · date range (Apply) · My location · From · Show. Columns: Transfer No (no wrap) · Date · From → To · Requisition No · Items (right) · Vehicle / Driver · Status (chip, centred) · Received (date · by) · Actions (right: **Receive** contained button or **View** text button). Paging footer; phone cards; skeletons; empty / no match / error / no location states.

## Receive page (`/admin/item-receive/receive?transferId=`)
```
PageHeader  Stock › Item Receive (link) › TRF/…            [←] Receive TRF/2026-27/0001  [In transit]
┌ card ─────────────────────────────────────────────────────────────────────┐
│ TRANSFER  Date · From Central Store → To Kitchen 1 · REQ/2026-27/0004       │
│           Vehicle MH12 AB 1234 · Driver Ravi · Contact … · Note …           │
│ RECEIVE   Receive date * [ ]                                                │
└────────────────────────────────────────────────────────────────────────────┘
┌ table: Item (pinned) · Unit · Requested · Sent · Received [ ] · Short · Reason [            ] ┐
Sticky footer: 5 items · 1 short                                 [Cancel] [Receive]
```
- Received prefilled with Sent; red outline + "Only 4 kg was sent" when over; Short shown in warning colour when > 0; Reason required (red outline + "Enter a reason") only when short.
- Confirm dialog before saving; discard prompt on leave and `beforeunload` when changed; skeletons while loading.
- Received transfer: same layout read-only, status chip *Received*, "Received on 03/11/2026 by Asha"; no footer buttons.
- 360 px: summary fields stack; table scrolls sideways with the item pinned; footer buttons full width.
- Dates in the company format; theme tokens only.
