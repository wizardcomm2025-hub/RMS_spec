# Stock Check — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `SCK-01-T01` | 01 | Open Stock → Stock Check as U1. | L1 selected; items listed; Count fields blank; system stock ONION 10 kg. |
| `SCK-01-T02` | 01 | Type 8 for ONION. | Variance "−2 kg (−20%)" in error colour; footer "1 item counted". |
| `SCK-01-T03` | 01 | With a typed count, switch tab. | "Discard counts?"; Keep stays; Discard switches. |
| `SCK-01-T04` | 01 | Show: With variance. | Only rows whose count differs from system. |
| `SCK-02-T01` | 02 | ONION 8 kg, OIL 5, leave SALT blank; Save counts (2). | "2 counts saved"; ONION system 8 kg, last count 8 kg now · U1; SALT unchanged. |
| `SCK-02-T02` | 02 | ONION 500 g (unit g); row ✓. | "Onion count saved"; system 0.5 kg; last count 0.5 kg. |
| `SCK-02-T03` | 02 | OIL 0; row ✓. | Saved; system 0 L. |
| `SCK-02-T04` | 02 | Count with note "spillage"; save; open Stock Check Report. | Note shown in the report. |
| `SCK-02-T05` | 02 | Footer button with no counts entered. | Button disabled. |
| `SCK-UI-T01` | UI | Page at 360 px. | Phone cards; no page sideways scroll. |

## 03 — Times in IST
| ID | Steps | Expected |
|---|---|---|
| `SC-03-T01` | Save a count at a known IST time (e.g. 15:40) | Last count shows today's date and 15:40 (company format) |
| `SC-03-T02` | Open Stock Check Report for today | The count is listed with Counted at 15:40; Export CSV shows the same date and time |
| `SC-03-T03` | Save a count between 00:00 and 05:30 IST; report for that date | Listed on that IST date, not the day before |
| `SC-03-T04` | Dashboard after counting a location today | That location is no longer in "not counted today" |
| `SC-03-T05` | Set the PC time zone to UTC, reload | Times still show in IST |
