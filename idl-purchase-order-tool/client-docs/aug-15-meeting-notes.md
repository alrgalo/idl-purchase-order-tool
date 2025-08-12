# PO Numbering Formats and Approval Workflow

## PO Numbering Formats

### Standard POs (≤ $50,000)
**Format:** `JJJJJ-PO-XXX`  
- `JJJJJ` – Job code (e.g., 301 for Job 4-25-301)  
- `XXX` – Sequential PO number  

**Example:** `25301-PO-023`  
→ Year 2025, Job 301, PO #23

---

### High-Value POs (Over $50,000)
**Format:** `JobNumber-PO-Sequence`  
**Example:** `4-10-061-M001`  
- `Job Number:` 4-10-061  
- `PO Sequence:` M001  

> Used for POs requiring subcontractor counter-signature and different Terms & Conditions.

---

## Roles and Access Control

- **User Account Creation:** Only finance personnel can create accounts (no public sign-ups).
- **Vendor Management:** Vendors will be managed by Armada Logics initially.
- **Job & Cost Code Management:**
  - Cost codes are job-specific and tied to allocated budgets.
  - Finance can upload budgets and assign users via the Job Management Interface.
  - Users will only see jobs they are assigned to.

---

## File Upload Support

- **Web:** Supports file uploads (with type and size restrictions).
- **Mobile:** Users can take and upload photos directly.

---

## PO Limits and Approval Flow

| Role             | Purchase Limit   |
|------------------|------------------|
| Foreman          | Up to $1,000     |
| Superintendent   | Up to $10,000    |
| Project Manager  | Up to $50,000    |
| Over $50,000     | Special handling |

- If a PO exceeds a user’s limit, it must be escalated to the next role.
- PO numbers are **only generated after approval**.

**Example:**  
A Foreman submitting a PO for **$10,001** requires **Project Manager** approval  
(Superintendent cap is $10,000).

- The system should send approval/rejection notifications.
- Optional rejection reasons should be supported.

---

## POs Over $50K: Special Handling

- Must use the `JobNumber-PO-Sequence` format.
- Requires **Project Manager** review and approval before issuance.
- Must follow alternate **Terms & Conditions** (examples attached).
- Requires **counter-signature** from the subcontractor.
- Limited to **supply-only** — installation, onsite work, or design must go through formal subcontract.
- The **PM Dashboard** should display all unsigned POs over $50K for follow-up.

---

## Draft PO Expiration

- Draft POs automatically expire after **10 days** of inactivity.
- System should send a **warning notification** before expiration.