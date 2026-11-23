# Purchase Order — tester test cases

| ID | Covers | Steps | Expected result |
|---|---|---|---|
| `PO-01-T01` | 01 | Open the list; change date range; Status Submitted; Vendor V1; search a number; page 2. | Server results; exact footer; Clear filters resets to this month. |
| `PO-02-T01` | 02 | New PO for V1 with RM1 ×5 @ 40; Save. | "Purchase order saved as draft"; number `PO/2026-27/000n` shown. |
| `PO-02-T02` | 02 | Edit D1: remove a line, close without saving; reopen. | Line still there. |
| `PO-02-T03` | 02 | Edit D1: remove the RL1 line, Save. | Total updated; RL1 back to pending in requisitions. |
| `PO-02-T04` | 02 | Submit D1. | "Purchase order submitted"; row shows Submitted; Edit gone. |
| `PO-02-T05` | 02 | Open S1. | Read-only with "This purchase order is submitted and can't be changed". |
| `PO-03-T01` | 03 | Cancel S1 without a reason; then with "Wrong vendor". | Reason required; then "Purchase order cancelled"; status Cancelled. |
| `PO-03-T02` | 03 | Hover Cancel on S2. | Disabled: "Goods received against this PO — can't be cancelled." |
| `PO-04-T01` | 04 | Delete D1. | "Purchase order deleted"; gone. |
| `PO-UI-T01` | UI | Vendor picker on a new PO; at 375 px. | V-OFF not offered; phone cards. |
| `PO-UI-T02` | UI | List → New purchase order; click "Purchase Orders" in the breadcrumb; repeat with ← and Cancel. | Back on the list each time; nothing saved. |
| `PO-UI-T03` | UI | New PO, pick a vendor, click Cancel. | "Discard changes?"; Keep editing stays; Discard returns to the list. |
| `PO-UI-T04` | UI | Click "Add item". | New row, cursor in its Item field; Items count +1. |
| `PO-UI-T05` | UI (on hold — requisition and scan buttons hidden) | "From requisition" → Add a line; "Scan invoice" opens. | Line added with REQ caption and qty; scan dialog opens. |
| `PO-UI-T06` | UI | Type an item name without picking; Save draft. | Row's Item field red "Pick an item from the list"; toast "Fix the highlighted items". |
| `PO-UI-T07` | UI | Valid PO → Submit purchase order. | Confirm "Submit purchase order to <vendor>? It can't be edited after this."; Submit → list with success toast. |
| `PO-UI-T08` | UI | Open a submitted PO. | Text-only fields, no toolbar / remove icons, footer "Back to list". |
| `PO-UI-T09` | UI | Form at 360 px. | Cards stack, buttons full width, table scrolls in its card, no page sideways scroll. |
| `PO-UI-T10` | UI | Look at the Actions of a Draft, a Submitted (nothing received), a Submitted with goods received, and a Cancelled PO. | Draft: View · Edit · Delete. Submitted: View · Cancel. Received: Cancel greyed, hover "Goods received against this PO — can't be cancelled." Cancelled: View only. |
| `PO-UI-T11` | UI | List → date filter: pick 01/Oct and 31/Oct, Apply; then a single day, Apply; then Clear, Apply. | Each time the list reloads for that range / day / all dates. |
| `PO-UI-T12` | UI | View a PO with 2 lines. | Dark title "Purchase order PO/…", status chip, total with separators, columns Item before Description, Amount per line, outlined Close. |

## 05 — Email to vendor
| ID | Steps | Expected |
|---|---|---|
| `PO-05-T01` | Open the list as U-ADMIN | ✉ "Email to vendor" on Submitted rows only; not on Draft or Cancelled |
| `PO-05-T02` | Click ✉ on a Submitted PO whose vendor has an email | Dialog "Email purchase order"; To = vendor email; CC = PO creator; subject "Purchase order {no} from {restaurant}"; 📎 `PO-…pdf` |
| `PO-05-T03` | Click Send | Snackbar "Purchase order emailed to vendor"; the vendor mailbox gets the mail with the PDF; the PDF shows restaurant, vendor, lines and total |
| `PO-05-T04` | Open the dialog again | "Last emailed {date time} to {email}" |
| `PO-05-T05` | Vendor with no email | Info "This vendor has no email. Add one in Vendor, or type an address."; Send disabled; type an address → Send enabled |
| `PO-05-T06` | Type `abc@` in To and Send | "Enter a valid email address: abc@"; nothing sent |
| `PO-05-T07` | Remove all To chips | "Add at least one email address" |
| `PO-05-T08` | Clear subject | "Enter a subject" |
| `PO-05-T09` | Type in Message, then ✕ | "Discard changes?" → Keep editing keeps the text |
| `PO-05-T10` | View dialog of a Submitted PO | "Email to vendor" button in the footer opens the same dialog |
| `PO-05-T11` | Phone width (360 px) | Full-screen dialog, chips wrap, no sideways scroll |
| `PO-05-T12` | User without `create_purchase_order` | No ✉ action and no footer button |
