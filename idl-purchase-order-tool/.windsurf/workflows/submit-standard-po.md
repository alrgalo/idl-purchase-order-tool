---
description: This workflow handles a one-time, supply-only purchase tied to a role-based approval limit. Used for predefined transactions like material or equipment orders.
---

Steps

Step 1: Draft P/O

Select IDL Project Number and Cost Code

Enter Vendor Name, Contact Info, and Item Line Details:

Item Description

Quantity

Unit

Unit Price

Category (LA/EQ/MA/SC)

System displays running total including tax and discount

Step 2: Validate Required Fields

Check all critical fields are filled

Ensure quantity and unit price are positive values

Show field-level errors with prompts for correction

Step 3: Check Role-Based Limit

Compare Total Amount against Purchaser’s role limit:

If within limit → allow submission

If over limit → initiate routing to Supervisor or PM

Step 4: Route for Approval (if over limit)

Tag approver with summary (P/O number, total, role, date)

Await decision (approve or reject with comments)

Step 5: Submit P/O

Lock all fields

Auto-generate P/O Number (Format: YYJJJ-PO-XYZ)

Save to JobCost and notify AP

Step 6: Notify Outcome

If approved → forward to AP and Vendor

If rejected → return to Purchaser with notes

Notes

Field validation is enforced before submission

Submitted P/Os are locked unless Admin unlocks

All actions are tracked in audit logs