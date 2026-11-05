# UI / UX — Inbound Requisition

## List
`PageHeader` "Stock › Inbound Requisition" (About links: Driver Master, Vehicle Master, Item Receive). Filter bar: search · date range (Apply) · My location · From · Show. Columns: Requisition No (no wrap) · Date · From → To · Items · Approved by · Left to send ("3 of 5" / "Fully sent" chip) · Transfers (🚚 counts) · Actions (Dispatch button, primary, small). Paging footer; phone cards. Loads on open (no "Get Request Items" button).

## Dispatch page (`/admin/inbound-requisition/dispatch?reqId=`)
```
PageHeader  Stock › Inbound Requisition (link) › REQ/…     [←] Dispatch REQ/2026-27/0004
┌ card ─────────────────────────────────────────────────────────────────────┐
│ REQUISITION  Date · From Kitchen 1 → To Central Store · Approved by Ravi    │
│ ───────────────────────────────────────────────────────────────────────── │
│ TRANSPORT  Vehicle * [▾]  Driver * [▾]  Person [ ]  Phone [ ]  Date * [ ]  │
│            Note [                                                     ]     │
└────────────────────────────────────────────────────────────────────────────┘
┌ table: Item (pinned) · Unit · Approved · Sent before · Remaining · In stock here · Send [ ] ┐
Sticky footer: 4 items to send                                  [Cancel] [Dispatch]
```
Send prefilled with min(remaining, stock); "Fully sent" rows greyed; inline red outline + "Only 4 kg left" / "Only 2 kg in stock" when over; confirm before dispatch; print dialog after; discard prompt on leave.
