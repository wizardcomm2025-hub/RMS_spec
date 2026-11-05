# Dashboard — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `DSH-01-T01` | 01 | Sign in as U-ADMIN; open Dashboard. | Six cards, Needs attention, chart, two lists; each card captioned "Right now" or "This period". |
| `DSH-01-T02` | 01 | Read Stock value and Low / out of stock; open Stock Report with the same location. | Same value and counts. |
| `DSH-01-T03` | 01 | Read the Sales card. | "₹600.00", "3 bills · average ₹200.00", "₹300.00 unpaid"; the O2 bill isn't included. |
| `DSH-01-T04` | 01 | Choose last month in dates; Apply. | Sales, Wastage, Production, chart and Top wasted change; Stock value, Low / out and Open POs don't. |
| `DSH-01-T05` | 01 | Choose location O1. | Everything follows O1 except Open purchase orders, still "All locations". |
| `DSH-01-T06` | 01 | Stop the API for one section (or block one request). | That section: "Couldn't load this." + Retry; no ₹0 shown; others load. |
| `DSH-01-T07` | 01 | Sign in as U-STAFF. | "Nothing to show for your role yet. Ask your admin for access to reports." |
| `DSH-01-T08` | 01 | Open at 360 px. | Two cards per row, sections stacked, lists as phone cards, no sideways scroll. |
| `DSH-01-T09` | 01 | Click Wastage. | Wastage Report opens. |
| `DSH-02-T01` | 02 | Read Needs attention. | "7 requisitions to approve", "6 transfers to receive", "2 purchase orders waiting for delivery", "1 draft purchase order", "1 location with no stock check today" (O1). |
| `DSH-02-T02` | 02 | Do a stock check at O1; refresh. | The stock check row disappears. |
| `DSH-02-T03` | 02 | Clear everything waiting. | "All caught up. Nothing is waiting for you." |
| `DSH-02-T04` | 02 | Click "6 transfers to receive". | Item Receive opens. |
