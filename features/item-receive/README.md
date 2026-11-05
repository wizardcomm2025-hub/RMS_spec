# Stock → Item Receive

**Item Receive** is the requesting location's side of a transfer: transfers **coming to my location** (dispatched in Inbound Requisition, status *In transit*), and **receiving** them. Receiving confirms what actually arrived per line, adds it to my location's stock and closes the transfer (status *Received*). Anything that didn't arrive is recorded as a **shortage** on the line with a reason.

Direction: `Transfer.FromWarehouseId` = sender (stock already went down at dispatch), `Transfer.ToWarehouseId` = receiver (stock goes up here on receive).

Status (2026-11-03): **Built — SQL not yet run on dev; testing deferred.** Plan: [`RMS_API/specs/item-receive/plan.md`](../../../RMS_API/specs/item-receive/plan.md). Developer guide: [`developer-guide.html`](./developer-guide.html).

Feature code: `IRC`

## Use cases

| # | File | Use case |
|---|---|---|
| 01 | [`01-view-and-search.md`](./01-view-and-search.md) | See transfers coming to my locations: in transit and received |
| 02 | [`02-receive.md`](./02-receive.md) | Receive a transfer (all lines at once, shortages with a reason); view a received one |

## Defects this feature fixes (found 2026-11-03)

| # | Defect | Fixed by |
|---|---|---|
| R1 | **A transfer can be received again**: the save never checks the status, so a second save (double click, two users, the mobile app) **adds the stock a second time** | [02](./02-receive.md) |
| R2 | Nothing is checked: received more than sent, negative quantities, lines from another transfer, a receive date in the future or before the transfer | [02](./02-receive.md) |
| R3 | Stock is read, increased and **written back** with no lock (a parallel Wastage / Stock Check / receive loses an update); the quantity is added **in the line's unit without conversion** (500 g arrives as +500 kg) and the stock row's **unit is overwritten** | [02](./02-receive.md) |
| R4 | No company / location check: any user can list, load (by transfer number) and receive **any company's** transfer | [01](./01-view-and-search.md), [02](./02-receive.md) |
| R5 | No transaction (cursor); a failure half-way leaves the transfer *Received* with only some lines added to stock. Lines left out are saved with nothing recorded — the missing goods vanish without a trace | [02](./02-receive.md) |
| R6 | The list returns **nothing** when To = "All" (`ToWarehouseId = 0` is matched literally); "From Warehouse" lists *my* locations but filters the sender; inactive locations show blank names; *Received at* shows today for transfers not yet received | [01](./01-view-and-search.md) |
| R7 | The mobile endpoints `GetReceiveList`, `GetTransferDetailsForRec`, `SaveReceivedData` need **no login** and take `UserId` / `CompanyId` from the request body | [02](./02-receive.md) |
| R8 | No server permission checks | all |
| R9 | "Get Items" button, no paging, type-the-transfer-number modal, receive qty defaults to blank, hex colours, not on the standard design | [01](./01-view-and-search.md), [02](./02-receive.md) |

## Decisions

| # | Decision | Date |
|---|---|---|
| Q1 | **Record shortage**: received < sent is allowed; the difference is a transit shortage on the line, a **reason is required**; it is not returned to the sender and the requisition is not reopened | 2026-11-03 |
| Q2 | **Block over-receipt**: received ≤ sent per line | 2026-11-03 |
| Q3 | **Receive once, all lines**: every line of the transfer is confirmed in one save (prefilled with the sent quantity); the transfer becomes *Received* and is locked | 2026-11-03 |
| Q4 | **Mobile endpoints secured and rerouted**: login token required, user / company from the token, saves go through the new receive (a mobile build that sends no token stops working until updated) | 2026-11-03 |
| D1 | List: transfers whose `To` is one of the user's locations, company of the caller; Show **In transit** (default) / Received / All; date range on the transfer date (default this month) | 2026-11-03 (default) |
| D2 | Receive date: today by default; not in the future, not before the transfer date | 2026-11-03 (default) |
| D3 | Quantities are in the transfer line's unit; stock goes up in the main unit (alternate ÷ conversion); a missing stock row at the receiver is created in the main unit | 2026-11-03 (default) |
| D4 | Permissions: `viewlist_item_receive` list / view, `receive_item_receive` receive (codes to be confirmed by the pre-check) | 2026-11-03 (default) |
| D5 | Old web endpoints (`GetTransferListForReceive`, `GetTransferDetailForReceive`, `SaveReceiveDetailsV2`) kept, company / location-guarded; `SaveReceiveDetailsV2` and `SaveReceivedData` go through the new receive with the shortage reason **not forced** (lines not sent count as 0) | 2026-11-03 (default) |
| D6 | The shortage reason is stored in `TransferDetail.ReceiveNote`; shortage = `TransferQty − ReceiveQty` (no schema change) | 2026-11-03 (default) |

## Out of scope
Returning a shortage to the sender or reopening the requisition; undoing a receive; yield (`YieldQty`, `Finalize`) — unchanged; Item Transfer (direct transfer without a requisition); correcting stock already doubled by R1 or wrongly converted by R3 (the pre-check lists suspects).
