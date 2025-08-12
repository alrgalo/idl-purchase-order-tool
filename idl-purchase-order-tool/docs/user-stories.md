# IDL Purchase Order Tool - User Stories

## Standard P/O Creation and Management

### P/O Generation
As a **Foreman**, I want to create a standard P/O up to $1,000 so that I can purchase necessary materials for my project while staying within my authorization limit.

As a **Super**, I want to create a standard P/O up to $10,000 so that I can procure materials and equipment needed for project operations within my approval authority.

As a **PM**, I want to create a standard P/O up to $50,000 so that I can authorize material purchases for my project while maintaining proper cost control.

As a **PM**, I want to review and approve all P/Os before they are issued so that I can ensure proper cost coding and vendor selection.

As a **Purchaser**, I want the system to issue a warning and request supervisor override when I exceed my approval limit so that I have flexibility for edge cases while maintaining oversight.

As a **Purchaser**, I want the system to prevent me from issuing a P/O number until all mandatory fields are completed so that AP receives complete information for invoice processing.

As a **Purchaser**, I want to be limited to active cost codes only for my selected project so that I cannot accidentally code costs to inactive or incorrect budget lines.

### P/O Information Management
As a **Purchaser**, I want to enter all critical header information (IDL Project Number, Project Location, Delivery Date, Vendor Contact Information) so that the P/O contains complete project and vendor details.

As a **Purchaser**, I want to enter mandatory purchase information for each line item (Item Description, Quantity, UoM, Cost Code, Category, Unit Price) so that the P/O provides complete material specifications.

As a **Purchaser**, I want to select from limited categories (LA, EQ, MA, SC) for each line item so that costs are properly categorized for accounting purposes.

As a **Purchaser**, I want an "apply to all" function for cost codes so that I can efficiently code multiple line items to the same cost code.

As a **Purchaser**, I want to add context notes for vendor communication (site contact, notice periods, equipment details) so that vendors have the information needed for successful delivery.

As a **Purchaser**, I want to add internal notes when mandatory fields are left blank so that there is accountability and explanation for incomplete P/Os.

## P/O Distribution and Communication

### Electronic P/O Issuance
As a **Purchaser**, I want the system to automatically send the full P/O as a PDF attachment to the vendor so that vendors can easily download and import into their systems.

As an **AP team member**, I want to automatically receive a copy of every issued P/O so that I can prepare for invoice matching and processing.

As a **Vendor**, I want to receive P/Os with complete information and AP contact details so that I can include the proper recipients when sending invoices.

### P/O Tracking and Visibility
As a **PM**, I want access to review all issued P/Os for my projects with status indicators (invoiced/pending) so that I can monitor project purchasing activity.

As a **PM**, I want to see P/O statistics (total values, number of P/Os, average values) so that I can identify purchasing patterns and potential planning issues.

As a **PM**, I want a dashboard showing outstanding unsigned P/Os over $50K so that I can follow up on required counter-signatures.

## Rolling P/O Management

### Rolling P/O Creation and Control
As a **Purchaser**, I want to create rolling P/Os for ongoing items (rentals, fuel) so that I can manage recurring purchases efficiently.

As a **PM**, I want to approve rolling P/O creation and any limit increases so that I maintain control over ongoing purchase commitments.

As a **Purchaser**, I want to see the remaining P/O value on rolling P/Os so that I know how much purchasing authority remains.

As a **PM**, I want to be notified when rolling P/Os are issued and when limits are increased so that I stay informed of ongoing purchase commitments.

## Walk-Up P/O Workflow

### Walk-Up P/O Creation
As a **Purchaser**, I want to create walk-up P/Os for hardware store purchases when materials are difficult to quote in advance so that I can make necessary purchases while maintaining proper documentation.

As a **Purchaser**, I want to provide vendor information, job number, and cost code before issuing a walk-up P/O so that the purchase is properly attributed to the correct project and budget line.

As a **Purchaser**, I want to select from predefined justifications or enter custom text for walk-up P/Os so that there is accountability and trend analysis capability for deviating from standard processes.

### Walk-Up P/O Completion
As a **Purchaser**, I want to upload vendor receipts/invoices as images and complete all line item coding to close walk-up P/Os so that AP has the complete documentation needed for processing.

As a **Purchaser**, I want to be locked out of creating future P/Os until my walk-up P/O is properly closed and coded so that I am incentivized to complete the documentation process promptly.

As a **Purchaser**, I want to code walk-up P/Os by line item after purchase so that costs are properly allocated to the correct budget categories.

## Quote and Document Management

### Quote Integration
As a **Purchaser**, I want to upload quotes (scanned, photographed, or digital) to auto-populate P/O fields so that I can create accurate P/Os efficiently.

As a **Purchaser**, I want to redact or cross out parts of vendor quotes so that vendor T&Cs don't override IDL's internal T&Cs.

As an **AP team member**, I want quotes attached to P/Os so that I can validate invoice pricing during the approval process.

### Supporting Documentation
As a **Purchaser**, I want to attach supporting documents (truck slips, receiving reports, photos) after purchase so that there is complete documentation of the transaction.

As a **Purchaser**, I want to upload packing slip images to the P/O file so that AP has delivery confirmation for invoice processing.

## Approval and Invoice Processing

### P/O Approval Workflow
As a **PM**, I want to review cost coding and category assignments for each line item so that I can ensure proper budget allocation.

As a **PM**, I want to validate costs against attached quotes so that I can confirm pricing accuracy before approval.

As a **PM**, I want to validate quantities against material receiving reports when available so that I can confirm delivery completeness.

### Invoice Matching
As an **AP team member**, I want P/Os and invoices automatically matched in the invoicing module so that I can process payments efficiently.

As an **AP team member**, I want pre-coded invoices based on accurate P/O information so that the approval process is streamlined and requires minimal recoding.

## System Integration and Data Management

### JobCost Integration
As a **PM**, I want the system to interface with JobCost so that P/O information flows into our cost management system.

As a **Purchaser**, I want access to active cost codes from JobCost based on my selected project so that I can only code to valid budget lines.

As a **PM**, I want to be notified when cost codes are left blank on P/Os so that I can provide the correct coding within 48 hours.

### Vendor Management
As a **Purchaser**, I want to select vendors from an active vendor list or manually enter new vendor information so that I can work with both established and new suppliers.

As a **Purchaser**, I want vendor contact information to auto-populate from Timberline when available so that I don't have to re-enter known vendor details.

As a **Purchaser**, I want to manually enter vendor contact details with optional AI autocomplete so that I have current contact information rather than outdated AR department contacts.

## P/O Authorization and Limits

### Authorization Controls
As a **Foreman**, I want my P/O creation limited to $1,000 so that I stay within my authorization level.

As a **Super**, I want my P/O creation limited to $10,000 so that I can handle most operational purchases within my authority.

As a **PM**, I want my P/O creation limited to $50,000 for standard P/Os so that larger purchases follow appropriate approval processes.

As a **PM**, I want P/Os over $50K to require different T&Cs and mandatory counter-signatures so that high-value purchases have appropriate contractual protection.

### Supply-Only Restrictions
As a **Purchaser**, I want standard P/Os limited to supply-only transactions so that installations, onsite work, and design services are properly handled through subcontracts.

As a **PM**, I want clear distinction between P/Os and subcontracts so that different types of vendor relationships are managed appropriately.

## P/O Numbering and Identification

### P/O Number Format
As a **Purchaser**, I want P/Os automatically numbered in YYJJJ-PO-XYZ format (e.g., 25301-PO-023) so that P/Os are clearly identified by year, job, and sequence.

As an **AP team member**, I want P/O numbers that clearly indicate the associated project so that I can easily match invoices to the correct job.

As a **PM**, I want P/O numbers that include my project identifier so that I can easily track all purchasing activity for my jobs.

## P/O Lifecycle Management

### P/O Modification and Cancellation
As a **Purchaser**, I want to cancel a submitted P/O before it's issued to the vendor so that I can correct errors or handle changed requirements.

As a **Purchaser**, I want to amend P/O details only while in draft status so that I can make corrections before final submission.

As a **System Administrator**, I want the system to prevent P/O cancellation or amendments after invoicing has occurred so that financial integrity is maintained.

### Post-Submission Documentation
As a **Purchaser**, I want to add load slips and delivery notes after P/O submission so that I can document delivery details without modifying the original P/O.

As a **Purchaser**, I want to append notes for vendor updates like backorders or staggered delivery so that all stakeholders are informed of changes.

As a **System Administrator**, I want original P/O details (line items, totals) to be locked after submission so that the core purchase information remains tamper-proof.
