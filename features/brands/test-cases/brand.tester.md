# Brand — tester test cases

A-ADMIN on HOST-A, Inventory → Brand, unless stated.

| ID | Covers | Steps | Expected result |
|---|---|---|---|
| `BRD-01-T01` | 01 AC 1–2 | Open the page. | Active brands A–Z then Old Brand; Coca-Cola shows chips "Thums Up", "Diet Coke" and Raw Materials **1**; Amul Raw Materials **1**; Status centred, Actions right. |
| `BRD-01-T02` | 01 AC 3 | Type "c", "coca"; Status Inactive; Clear filters. | Narrows from the first letter; Inactive + "coca" → no-match; Clear → all. |
| `BRD-01-T03` | 01 AC 4 | Offline → Refresh → online → Retry. | "We couldn't load your brands. Check your connection and try again."; Retry loads. |
| `BRD-01-T04` | 01 AC 6 | Log in as A-VIEW. | No Add, Edit or switch. |
| `BRD-02-T01` | 02 AC 1 | **＋ Add Brand**; "Nestle"; save. | "Brand created"; listed Active. |
| `BRD-02-T02` | 02 AC 2 | Add " amul "; add "old brand". | "Brand name already exists" each; no success toast. |
| `BRD-02-T03` | 02 | Add "Brand@1"; type 105 characters. | Characters message; field stops at 100. |
| `BRD-02-T04` | 02 AC 1, 5 | Edit Fortune → "Fortune Foods"; then A-ADMIN2 edits it while A-ADMIN's dialog is open; A-ADMIN saves. | "Brand updated"; then "This brand was changed by someone else. Reload and try again." |
| `BRD-03-T01` | 03 AC 1 | Turn off Fortune; read; Deactivate. | "Deactivate **Fortune**? It can't be picked for raw materials any more. You can activate it again at any time." → "Brand deactivated". |
| `BRD-03-T02` | 03 AC 2 | Turn off Coca-Cola; then Amul. | "This brand has active sub-brands. Switch them off first."; "This brand is used by active raw materials. Move them first." |
| `BRD-03-T03` | 03 AC 3 | After T01 open Raw Material → Add → Brand. | Fortune not offered. |
| `BRD-03-T04` | 03 | Turn Old Brand on → Activate. | "Activate **Old Brand**?" → "Brand activated". |
