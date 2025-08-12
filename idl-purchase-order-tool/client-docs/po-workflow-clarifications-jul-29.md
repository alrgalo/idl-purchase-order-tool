# P/O Tool Workflow Clarifications

Hi All,

Ahead of our upcoming call, I’ve added comments and responses to several open questions regarding the Purchase Order (P/O) Tool. I may be a few minutes late to the invoicing discussion but will join in time for the P/O segment.

## Workflow Questions & Edge Case Clarifications

### 1. Should approval limits strictly block over-budget P/Os, or just issue a warning?
- ✅ **Decision**: Issue a warning with a request for supervisor override.
- **Rationale**: Flexibility allows for faster processing in edge cases, while still maintaining oversight.

---

### 2. Can a submitted P/O be cancelled or amended before approval? Who has permission?
- ⚠️ **For Discussion**: Concern about misuse—users skipping workflow just to generate a P/O number and attach an invoice.
- ✅ **Cancelling**: Allowed **if** the P/O has not yet been issued to the vendor.
- ✅ **Amendments**: Allowed **only** if the P/O is still in draft and not yet submitted.
- ❌ **After Invoicing**: Cancellation or amendments should be **disallowed** once invoicing has occurred.

---

### 3. Once submitted, should attachments and notes still be editable?
- ✅ **Decision**: Allow limited editing.
- **Details**:
  - Purchasers should be able to **add** load slips or delivery notes after submission.
  - Original P/O details (line items, totals) should remain locked after submission.
  - Notes can be appended for vendor updates like backorders or staggered delivery.

---

### 4. What signals that a walk-up P/O is “closed”?
- ✅ **Definition**: A walk-up P/O is considered "closed" when:
  - Receipt is uploaded.
  - Line items are coded.
  - All required fields are complete.
  - Invoice has been received.
- 📝 **Note**: Being editable does not imply the P/O is still open.

---

### 5. For walk-up P/Os, should users be able to reuse standard justifications?
- ✅ **Decision**: Use a **combo input** with:
  - Predefined dropdowns for trend analysis and policy insights.
  - Free-text field for case-specific context to assist the PM.
- **Rationale**: Avoids boilerplate or meaningless entries while improving data quality.

---

### 6. For vendor emails, should the system send the full P/O as a PDF attachment or just a summary link?
- ✅ **Decision**: Send the full P/O as a **PDF attachment**.
- **Rationale**:
  - Easier for vendors to download and import into their systems.
  - Avoids issues with firewalls or restricted link access.

---

Please review before the meeting and feel free to add any follow-up comments directly.
