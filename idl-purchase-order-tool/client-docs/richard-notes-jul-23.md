# Internal Purchase Order (P/O) Notes

**Good Morning Folks,**

Some notes from our internal P/O discussion are shared below.

The list is not exhaustive and James may have additional notes. These notes focus on **Phase 1 buildout**. Future phases may require internal alignment before sharing further scoping considerations.

---

## Phase 1

### Three Categories of P/O

#### 1. Standard P/O with Limits
- $1,000 – Foreman  
- $10,000 – Super  
- $50,000 – PM  
- Over $50K:
  - Either:
    - **Subcontract** – *outside the scope of this tool*
    - **P/O with different T&Cs** – *examples attached*
      - Must include **mandatory counter-signature** from subcontractor.
- **PM approval/review required** prior to issuing.
- Limited to **supply only**. Installations, onsite work, or design should fall under **subcontract**.
- **PM Dashboard** should display outstanding unsigned P/Os over $50K for follow-up.

#### 2. Rolling P/O with Limit
- Requires **PM approval to increase** (completed in system).
- Used for ongoing items (e.g. long-term rentals, fuel).
- **Notify PM** when:
  - Issued
  - Limit is increased
- If possible, system should display **remaining P/O value**.

#### 3. Walk-Up P/Os
- For hardware store purchases (e.g. Rona, Home Hardware).
- Used when materials are difficult to quote in advance.
- **Issued before** line items are entered.
- User must provide:
  - Vendor information
  - Job number
  - Cost code
- Vendor must provide **receipt/invoice**, which is uploaded as an image.
- User is **locked out of future P/Os** until walk-up P/O is:
  - Closed with invoice
  - Properly coded by line item
- Internal note required to justify why walk-up was used instead of normal workflow.
  - Reviewed during approval.
- Walk-ups are discouraged by default, but allowed when necessary.
- Should support **photo/quote upload** to auto-populate P/O.
  - Encourages better behavior and streamlines **invoice validation**.
- Should support **redacting/crossing out** parts of vendor quotes.
  - Prevents vendor T&Cs from overriding internal T&Cs.

---

## Future Buildout (Planning)

- Ability to **request and compare quotes** through the system.
- Review, select, and store quotes for **later P/O conversion** (like BuildingConnected).
- Feature to **store estimate quotes** until the project is underway.

---

## Required Fields and Rules

- Vendor Info:
  - **Company Name**
  - **Contact Name**
  - **Contact Phone**
  - **Contact Email**
  - Skip branch, fax, and location details.
  - Vendor name can pull from Timberline.
  - Contact details should be **manually entered**, with optional **AI autocomplete**.
    - Do **not** populate contact from Timberline (usually AR dept).

- **P/O Number Format**: `YYJJJ-PO-XYZ`
  - Example: `25301-PO-023`
    - Means: Job 25-301, PO #23

- Option to **attach supporting documents** post-purchase:
  - Truck slips
  - Receiving reports
  - Photos

- **Counter-signature requirement**:
  - **Mandatory** for P/Os over $50K
  - **Optional/Low-probability** for P/Os below $10K or standard vendors

---