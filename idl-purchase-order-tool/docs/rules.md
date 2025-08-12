# Purchase Order Rules

## Draft Expiry

- Draft POs automatically expire after **10 days** of inactivity.
- A system notification will be sent **2 days before expiration**.

## PO Number Format

### Standard POs (≤ ₱50,000)

**Format:** YYJJJ-PO-XXX  
- `YY`: Year (e.g., `25` for 2025)  
- `JJJ`: Job code (e.g., `301` for Job 4-25-301)  
- `XXX`: Sequential PO number (zero-padded)  

**Example:** `25301-PO-023` → 2025, Job 301, PO #23

### High-Value POs (> ₱50,000)

**Format:** YYJJJ-HPO-XXX  
Same format as above, but with `HPO` instead of `PO`.

## Role-Based Approval Thresholds

| Role            | Approval Limit |
|-----------------|----------------|
| Foreman         | ₱1,000         |
| Superintendent  | ₱10,000        |
| Project Manager | ₱50,000        |

- POs exceeding the role’s threshold will be routed to the next higher approver.

## Post-Submission Edits

Once a PO is submitted:
- **Only the following fields remain editable**:
  - **Notes**
  - **Document attachments**
- All other fields are locked and require cancellation + re-creation to modify.

---

## 🖱 UI & Behavior – Purchase Order Tool

### General Interaction

- All interactive elements (buttons, links, icons) must show a `cursor: pointer` on hover.
- Forms support `Enter` to submit and `Esc` to cancel where applicable.
- Tables must support:
  - Row-level hover highlights
  - Column resizing (if enabled)
- Tooltips must be used for:
  - Truncated text
  - Non-obvious icons

---

### Field Behavior

- Required fields must show an asterisk `*`.
- Numeric fields (quantities, unit cost) must:
  - Right-align
  - Disallow non-numeric input
- Date fields use a calendar picker (no manual text entry).
- Amount fields auto-format as currency (₱X,XXX.00) after blur.
- Status chips (e.g., Draft, Pending, Approved, Expired) must use consistent color coding.