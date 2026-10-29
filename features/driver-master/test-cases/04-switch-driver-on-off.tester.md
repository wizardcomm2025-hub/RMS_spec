# Tester Test Cases — 04 Switch Driver On / Off

---

## Tests

| ID | AC | Persona | Action / Steps | Expected UI Result |
|---|---|---|---|---|
| `DRV-04-T01` | AC 1, 2 | `A-FULL` | Click the switch on an active driver (e.g. `DRV_1`). | Confirmation dialog opens: Title "Deactivate driver", Message: "Deactivate **Rajesh Kumar**? Staff won't be able to assign this driver to delivery or stock transfers. Past transfers keep this driver. You can activate them again at any time." Buttons: Cancel and **Deactivate**. |
| `DRV-04-T02` | AC 2 | `A-FULL` | In the deactivate dialog, click "Cancel". | Dialog closes. The switch remains in the Active (checked) position. No API call made. |
| `DRV-04-T03` | AC 2, 4 | `A-FULL` | In the deactivate dialog, click **Deactivate**. | Confirm button shows spinner; on completion, modal closes, toast displays **"Driver deactivated"**, the switch moves to unchecked, and status chip shows "Inactive". |
| `DRV-04-T04` | AC 3 | `A-FULL` | Click the switch on an inactive driver. | Confirmation dialog opens: Title "Activate driver", Message: "Activate **Rajesh Kumar**?" Buttons: Cancel and **Activate**. |
| `DRV-04-T05` | AC 3, 4 | `A-FULL` | In the activate dialog, click **Activate**. | Confirm button shows spinner; on completion, modal closes, toast displays **"Driver activated"**, the switch moves to checked, and status chip shows "Active". |
| `DRV-04-T06` | AC 4 | `A-FULL` | Toggle switch when network is disconnected. | Modal closes, toast displays error message, and switch returns to its previous state. |
| `DRV-04-T07` | AC 5 | `A-FULL` | Inspect the actions column and modal. | There is **no Delete button or delete action anywhere**. Master records cannot be deleted. |
| `DRV-04-T08` | Q5 | `A-FULL` | Deactivate Amit Singh. Open Stock → Inbound Requisition, start a dispatch and open the Driver dropdown. | Amit Singh is not offered; a past transfer that used him still shows his name. |
