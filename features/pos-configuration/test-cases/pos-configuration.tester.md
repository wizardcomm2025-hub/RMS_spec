# POS Configuration — tester test cases

| ID | Use case | Steps | Expected (on screen) |
|---|---|---|---|
| `POS-01-T01` | 01 | Open POS Configuration → Tax Setting as U-ADMIN. | GST 5% with *Default*, GST 18%, VAT 12% *Inactive*; totals 5%, 18%, 12%. |
| `POS-01-T02` | 01 | Status: Inactive. | Only VAT 12%. |
| `POS-02-T01` | 02 | Add tax group "GST 12%": CGST 6, SGST 6; Save. | "Tax group added"; listed with total 12%; not default. |
| `POS-02-T02` | 02 | Add "gst 5%". | "A tax group with this name already exists". |
| `POS-02-T03` | 02 | Add a component "Cess, state#1" 1%; Save. | Saved; the name shows exactly as typed. |
| `POS-02-T04` | 02 | Percentage 100. | "Enter a percentage from 0 to 99.99". |
| `POS-02-T05` | 02 | Components 60 + 45. | "The total must be under 100%". |
| `POS-02-T06` | 02 | Edit GST 18%, remove SGST, Save, reopen. | SGST isn't on the form; total 9%. |
| `POS-02-T07` | 02 | Type in the dialog, press ✕. | "Discard changes?" with Keep editing / Discard. |
| `POS-03-T01` | 03 | ⋮ on GST 18% → Make default; confirm. | "GST 18% is now the default"; the chip moves. |
| `POS-03-T02` | 03 | ⋮ on the default group. | Deactivate disabled with "Make another group the default first". |
| `POS-03-T03` | 03 | Deactivate GST 18% (not default); confirm. | "GST 18% deactivated"; chip *Inactive*. |
| `POS-03-T04` | 03 | Activate VAT 12%. | "VAT 12% activated". |
| `POS-04-T01` | 04 | With GST 5% default and GST 18% active, bill ₹1,000 on the POS. | Tax ₹50; bill shows GST 5%. |
| `POS-04-T02` | 04 | Make GST 18% default; bill ₹1,000 again; reopen the first bill. | New bill tax ₹180; the first bill still shows ₹50. |
| `POS-01-T03` | 01 | Sign in as U-VIEW. | List shown; no Add, Edit or ⋮. |
| `POS-05-T01` | 05 | Open Order Channels. | Table 5 seats 4; Takeaway seats "—". |
| `POS-05-T02` | 05 | Search "t". | Results after one letter. |
| `POS-05-T03` | 05 | Add channel type Takeaway at Patio, no seats. | Saves; Seats field not shown. |
| `POS-05-T04` | 05 | Add type Table without seats. | "Seats is required". |
| `POS-05-T05` | 05 | Turn Table 5's Active off. | "Turn off Table 5?"; Cancel leaves it on. |
| `POS-05-T06` | 05 | Outlet dropdown in filter and form. | Main Hall and Patio only (not CK). |
| `POS-05-T07` | 05 | Stop the POS API; refresh. | "We couldn't load order channels." + Retry. |
| `POS-UI-T01` | UI | 360 px and 1280 px on both screens. | Cards on the phone; tables with no sideways scroll. |
| `POS-UI-T02` | UI | Role screen → POS Configurations. | Tax Setting and Order Channel View offer View, Create and Edit. |
