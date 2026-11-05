# Item Receive — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `IRC-01-T01` | 01 | Open Stock → Item Receive as U1. | T1 listed *In transit* with a **Receive** button; T2 and T3 not listed (Show: In transit). |
| `IRC-01-T02` | 01 | Show: All. | T1 and T2 listed; T2 *Received* with date and name; T3 never listed. |
| `IRC-02-T01` | 02 | Receive T1. | ONION sent 6 kg, Received prefilled 6; TOMATO sent 500 g, Received 500; receive date today. |
| `IRC-02-T02` | 02 | ONION Received 7. | Red outline; "Only 6 kg of Onion was sent". |
| `IRC-02-T03` | 02 | ONION Received 5, no reason; Receive. | "Enter a reason for the shortage of Onion". |
| `IRC-02-T04` | 02 | Reason "Spilled"; Receive; confirm. | "Transfer TRF/2026-27/0001 received"; list shows T1 *Received · 1 short*; K1 ONION stock 7 kg, TOMATO +0.5 kg. |
| `IRC-02-T05` | 02 | View T1. | Read-only: ONION received 5, short 1, reason "Spilled"; no Receive button. |
| `IRC-02-T06` | 02 | Receive date tomorrow. | "Receive date can't be in the future". |
| `IRC-UI-T01` | UI | List and receive page at 360 px. | Phone cards; summary stacks; item column pinned; no page sideways scroll. |
