# Sub Brand — tester test cases

A-ADMIN on HOST-A, Inventory → Sub Brand, unless stated.

| ID | Covers | Steps | Expected result |
|---|---|---|---|
| `BRD-04-T01` | 04 AC 1 | Open the page. | Thums Up, Diet Coke and Old Sub (under "Old Brand (inactive)") all listed; Thums Up Raw Materials **1**. |
| `BRD-04-T02` | 04 AC 2 | Open the browser's Network tab, reload the page. | One sub-brand list request — no burst of item requests. |
| `BRD-04-T03` | 04 AC 3 | Brand filter Coca-Cola + Status Active + search "diet". | Only Diet Coke. |
| `BRD-05-T01` | 05 AC 1 | **＋ Add Sub Brand**; Brand Coca-Cola; "Coke Zero"; save. | "Sub-brand created". |
| `BRD-05-T02` | 05 AC 2 | Add " diet coke " under Coca-Cola; then under Fortune. | First: "Sub-brand name already exists under this brand"; second: created. |
| `BRD-05-T03` | 05 AC 3 | Open the Brand list in the dialog. | Old Brand is not offered. |
| `BRD-05-T04` | 05 AC 4 | Edit Diet Coke → Brand Fortune → save; edit Thums Up → Brand Fortune → save. | First saved; second "This sub-brand is used by active raw materials, so its brand can't be changed." |
| `BRD-06-T01` | 06 AC 1 | Turn off Diet Coke → Deactivate; turn it on → Activate. | "Sub-brand deactivated"; it stays listed as Inactive; then "Sub-brand activated". |
| `BRD-06-T02` | 06 AC 2 | Turn off Thums Up. | "This sub-brand is used by active raw materials. Move them first." |
| `BRD-06-T03` | 06 AC 3 | Turn on Old Sub. | "Switch its brand on first." |
| `BRD-06-T04` | UI | As A-VIEW; at 375 px. | No Add / Edit / switch; phone cards. |
