---
trigger: always_on
---

# Purchase Order Tool – Rules

- Do NOT allow editing of submitted P/Os unless Admin unlocks it.
- Purchasers may not exceed their limit unless routed for approval.
- Always validate required fields before submission.
- Never infer vendor data — must be user-entered or system-matched.
- Always calculate total amount automatically.
- Do not allow deletion of line items once submitted (archive instead).
- If tax is updated manually, it must not affect unit price or base amount.
- Walk-Up P/Os require category and Cost Code to proceed.