## P/O Number Format
- Standard P/Os (≤ $50,000):
  - Format: JJJJJ-PO-XXX
  - JJJJJ = Job code (e.g., 25301 for Job 4-25-301)
  - XXX = Sequential PO number
  - P/O number generated only after approval
- High-Value P/Os (Over $50,000):
  - Format: JobNumber-PO-Sequence
  - Example: 4-10-061-M001
  - Requires subcontractor counter-signature
  - Uses different Terms & Conditions
  - Limited to supply-only purchases
  - No installation, onsite work, or design (requires formal subcontract)

## Business Rules
### Access Control Rules
- Only finance personnel can create user accounts
- Vendors managed by Armada Logics initially
- Users can only see jobs they are assigned to
- Cost codes are job-specific and tied to allocated budgets
- Finance uploads budgets and assigns users via Job Management Interface

### Authorization Limits
- Foreman: Up to $1,000
- Superintendent: Up to $10,000
- Project Manager: Up to $50,000
- Over $50,000: Special handling required
- P/Os exceeding user's limit require escalation to next role

### Draft Management
- Draft P/Os expire after 10 days of inactivity
- System sends warning notification before expiration
- PM Dashboard shows all unsigned P/Os over $50K

### File Management
- Web interface supports file uploads (with restrictions)
- Mobile interface supports direct photo capture
- File types and sizes must be validated

### Approval Workflow
- System must send approval/rejection notifications
- Optional rejection reasons supported
- P/Os over $50K require PM review before issuance
- Counter-signature required for P/Os over $50K
