# IDL Purchase Order Tool - Product Backlog

## Epic 1: Core P/O Creation and Management

### Standard P/O Workflow
**Priority: High**

**PO-001** As a **Foreman**, I want to create a standard P/O up to $1,000 so that I can purchase necessary materials for my project while staying within my authorization limit.
- **Acceptance Criteria:** System enforces $1,000 limit, validates role permissions
- **Story Points:** 8

**PO-002** As a **Super**, I want to create a standard P/O up to $10,000 so that I can procure materials and equipment needed for project operations within my approval authority.
- **Acceptance Criteria:** System enforces $10,000 limit, validates role permissions
- **Story Points:** 5

**PO-003** As a **PM**, I want to create a standard P/O up to $50,000 so that I can authorize material purchases for my project while maintaining proper cost control.
- **Acceptance Criteria:** System enforces $50,000 limit, validates role permissions
- **Story Points:** 5

**PO-004** As a **Purchaser**, I want the system to issue a warning and request supervisor override when I exceed my approval limit so that I have flexibility for edge cases while maintaining oversight.
- **Acceptance Criteria:** Warning dialog, supervisor override workflow, audit trail
- **Story Points:** 13

**PO-004A** As a **Purchaser**, I want the system to prevent me from issuing a P/O number until all mandatory fields are completed so that AP receives complete information for invoice processing.
- **Acceptance Criteria:** Field validation, error messages, P/O number generation blocked until complete
- **Story Points:** 8

**PO-005** As a **Purchaser**, I want to be limited to active cost codes only for my selected project so that I cannot accidentally code costs to inactive or incorrect budget lines.
- **Acceptance Criteria:** Integration with JobCost, dynamic cost code filtering
- **Story Points:** 21

### P/O Information Management
**Priority: High**

**PO-006** As a **Purchaser**, I want to enter all critical header information (IDL Project Number, Project Location, Delivery Date, Vendor Contact Information) so that the P/O contains complete project and vendor details.
- **Acceptance Criteria:** Form validation, required field indicators, data persistence
- **Story Points:** 8

**PO-007** As a **Purchaser**, I want to enter mandatory purchase information for each line item (Item Description, Quantity, UoM, Cost Code, Category, Unit Price) so that the P/O provides complete material specifications.
- **Acceptance Criteria:** Line item management, calculation engine, validation rules
- **Story Points:** 13

**PO-008** As a **Purchaser**, I want to select from limited categories (LA, EQ, MA, SC) for each line item so that costs are properly categorized for accounting purposes.
- **Acceptance Criteria:** Dropdown with predefined categories, validation
- **Story Points:** 3

**PO-009** As a **Purchaser**, I want an "apply to all" function for cost codes so that I can efficiently code multiple line items to the same cost code.
- **Acceptance Criteria:** Bulk update functionality, confirmation dialog
- **Story Points:** 5

**PO-010** As a **Purchaser**, I want to add context notes for vendor communication (site contact, notice periods, equipment details) so that vendors have the information needed for successful delivery.
- **Acceptance Criteria:** Text field with character limits, formatting options
- **Story Points:** 3

## Epic 2: P/O Distribution and Communication

### Electronic P/O Issuance
**Priority: High**

**PO-011** As a **Purchaser**, I want the system to automatically send the full P/O as a PDF attachment to the vendor so that vendors can easily download and import into their systems.
- **Acceptance Criteria:** Email automation, PDF generation, attachment functionality, template management
- **Story Points:** 21

**PO-012** As an **AP team member**, I want to automatically receive a copy of every issued P/O so that I can prepare for invoice matching and processing.
- **Acceptance Criteria:** Automatic CC to AP email, notification system
- **Story Points:** 8

**PO-013** As a **Vendor**, I want to receive P/Os with complete information and AP contact details so that I can include the proper recipients when sending invoices.
- **Acceptance Criteria:** Complete vendor contact info, AP details in P/O template
- **Story Points:** 5

### P/O Tracking and Visibility
**Priority: Medium**

**PO-014** As a **PM**, I want access to review all issued P/Os for my projects with status indicators (invoiced/pending) so that I can monitor project purchasing activity.
- **Acceptance Criteria:** Dashboard view, filtering by project, status tracking
- **Story Points:** 13

**PO-015** As a **PM**, I want to see P/O statistics (total values, number of P/Os, average values) so that I can identify purchasing patterns and potential planning issues.
- **Acceptance Criteria:** Analytics dashboard, charts and graphs, export functionality
- **Story Points:** 21

**PO-016** As a **PM**, I want a dashboard showing outstanding unsigned P/Os over $50K so that I can follow up on required counter-signatures.
- **Acceptance Criteria:** Filtered view, alert system, aging reports
- **Story Points:** 13

## Epic 3: Rolling P/O Management

### Rolling P/O Creation and Control
**Priority: Medium**

**PO-017** As a **Purchaser**, I want to create rolling P/Os for ongoing items (rentals, fuel) so that I can manage recurring purchases efficiently.
- **Acceptance Criteria:** Rolling P/O type selection, ceiling amount setup, usage tracking
- **Story Points:** 21

**PO-018** As a **PM**, I want to approve rolling P/O creation and any limit increases so that I maintain control over ongoing purchase commitments.
- **Acceptance Criteria:** Approval workflow, limit modification controls, audit trail
- **Story Points:** 13

**PO-019** As a **Purchaser**, I want to see the remaining P/O value on rolling P/Os so that I know how much purchasing authority remains.
- **Acceptance Criteria:** Real-time balance display, threshold alerts
- **Story Points:** 8

**PO-020** As a **PM**, I want to be notified when rolling P/Os are issued and when limits are increased so that I stay informed of ongoing purchase commitments.
- **Acceptance Criteria:** Email notifications, dashboard alerts, summary reports
- **Story Points:** 8

## Epic 4: Walk-Up P/O Workflow

### Walk-Up P/O Creation
**Priority: Medium**

**PO-021** As a **Purchaser**, I want to create walk-up P/Os for hardware store purchases when materials are difficult to quote in advance so that I can make necessary purchases while maintaining proper documentation.
- **Acceptance Criteria:** Walk-up P/O type, simplified initial form, justification requirement
- **Story Points:** 13

**PO-022** As a **Purchaser**, I want to provide vendor information, job number, and cost code before issuing a walk-up P/O so that the purchase is properly attributed to the correct project and budget line.
- **Acceptance Criteria:** Minimal required fields, validation rules
- **Story Points:** 8

**PO-023** As a **Purchaser**, I want to select from predefined justifications or enter custom text for walk-up P/Os so that there is accountability and trend analysis capability for deviating from standard processes.
- **Acceptance Criteria:** Combo input with dropdown and free-text, mandatory selection, approval review
- **Story Points:** 8

### Walk-Up P/O Completion
**Priority: High**

**PO-024** As a **Purchaser**, I want to upload vendor receipts/invoices as images and complete all line item coding to close walk-up P/Os so that AP has the complete documentation needed for processing.
- **Acceptance Criteria:** Image upload, line item coding interface, completion validation, status tracking
- **Story Points:** 13

**PO-025** As a **Purchaser**, I want to be locked out of creating future P/Os until my walk-up P/O is properly closed and coded so that I am incentivized to complete the documentation process promptly.
- **Acceptance Criteria:** User lockout mechanism, completion tracking, override permissions
- **Story Points:** 13

**PO-026** As a **Purchaser**, I want to code walk-up P/Os by line item after purchase so that costs are properly allocated to the correct budget categories.
- **Acceptance Criteria:** Line item coding interface, validation against receipts
- **Story Points:** 8

## Epic 5: Quote and Document Management

### Quote Integration
**Priority: Medium**

**PO-027** As a **Purchaser**, I want to upload quotes (scanned, photographed, or digital) to auto-populate P/O fields so that I can create accurate P/Os efficiently.
- **Acceptance Criteria:** File upload, OCR processing, field mapping, manual override
- **Story Points:** 34

**PO-028** As a **Purchaser**, I want to redact or cross out parts of vendor quotes so that vendor T&Cs don't override IDL's internal T&Cs.
- **Acceptance Criteria:** Document annotation tools, redaction functionality
- **Story Points:** 21

**PO-029** As an **AP team member**, I want quotes attached to P/Os so that I can validate invoice pricing during the approval process.
- **Acceptance Criteria:** Document attachment system, version control
- **Story Points:** 8

### Supporting Documentation
**Priority: Low**

**PO-030** As a **Purchaser**, I want to attach supporting documents (truck slips, receiving reports, photos) after purchase so that there is complete documentation of the transaction.
- **Acceptance Criteria:** Multi-file upload, document categorization, search functionality
- **Story Points:** 13

**PO-031** As a **Purchaser**, I want to upload packing slip images to the P/O file so that AP has delivery confirmation for invoice processing.
- **Acceptance Criteria:** Image upload, file organization, AP notification
- **Story Points:** 8

## Epic 6: Approval and Invoice Processing

### P/O Approval Workflow
**Priority: High**

**PO-032** As a **PM**, I want to review and approve all P/Os before they are issued so that I can ensure proper cost coding and vendor selection.
- **Acceptance Criteria:** Approval queue, review interface, approval/rejection workflow
- **Story Points:** 21

**PO-033** As a **PM**, I want to review cost coding and category assignments for each line item so that I can ensure proper budget allocation.
- **Acceptance Criteria:** Line-by-line review interface, editing capabilities
- **Story Points:** 13

**PO-034** As a **PM**, I want to validate costs against attached quotes so that I can confirm pricing accuracy before approval.
- **Acceptance Criteria:** Side-by-side comparison view, variance highlighting
- **Story Points:** 13

**PO-035** As a **PM**, I want to validate quantities against material receiving reports when available so that I can confirm delivery completeness.
- **Acceptance Criteria:** Quantity comparison, discrepancy reporting
- **Story Points:** 13

### Invoice Matching
**Priority: High**

**PO-036** As an **AP team member**, I want P/Os and invoices automatically matched in the invoicing module so that I can process payments efficiently.
- **Acceptance Criteria:** Automated matching algorithm, exception handling
- **Story Points:** 34

**PO-037** As an **AP team member**, I want pre-coded invoices based on accurate P/O information so that the approval process is streamlined and requires minimal recoding.
- **Acceptance Criteria:** Automatic coding transfer, validation rules
- **Story Points:** 21

## Epic 7: System Integration and Data Management

### JobCost Integration
**Priority: High**

**PO-038** As a **PM**, I want the system to interface with JobCost so that P/O information flows into our cost management system.
- **Acceptance Criteria:** API integration, data synchronization, error handling
- **Story Points:** 34

**PO-039** As a **Purchaser**, I want access to active cost codes from JobCost based on my selected project so that I can only code to valid budget lines.
- **Acceptance Criteria:** Real-time cost code lookup, project filtering
- **Story Points:** 21

**PO-040** As a **PM**, I want to be notified when cost codes are left blank on P/Os so that I can provide the correct coding within 48 hours.
- **Acceptance Criteria:** Notification system, escalation workflow, tracking
- **Story Points:** 13

### Vendor Management
**Priority: Medium**

**PO-041** As a **Purchaser**, I want to select vendors from an active vendor list or manually enter new vendor information so that I can work with both established and new suppliers.
- **Acceptance Criteria:** Vendor database, search functionality, new vendor creation
- **Story Points:** 13

**PO-042** As a **Purchaser**, I want vendor contact information to auto-populate from Timberline when available so that I don't have to re-enter known vendor details.
- **Acceptance Criteria:** Timberline integration, data mapping, fallback options
- **Story Points:** 21

**PO-043** As a **Purchaser**, I want to manually enter vendor contact details with optional AI autocomplete so that I have current contact information rather than outdated AR department contacts.
- **Acceptance Criteria:** Smart autocomplete, contact validation, data quality checks
- **Story Points:** 13

## Epic 8: P/O Authorization and Limits

### Authorization Controls
**Priority: High**

**PO-044** As a **Foreman**, I want my P/O creation limited to $1,000 so that I stay within my authorization level.
- **Acceptance Criteria:** Role-based limits, validation, override workflow
- **Story Points:** 8

**PO-045** As a **Super**, I want my P/O creation limited to $10,000 so that I can handle most operational purchases within my authority.
- **Acceptance Criteria:** Role-based limits, validation, override workflow
- **Story Points:** 5

**PO-046** As a **PM**, I want my P/O creation limited to $50,000 for standard P/Os so that larger purchases follow appropriate approval processes.
- **Acceptance Criteria:** Role-based limits, escalation workflow
- **Story Points:** 5

**PO-047** As a **PM**, I want P/Os over $50K to require different T&Cs and mandatory counter-signatures so that high-value purchases have appropriate contractual protection.
- **Acceptance Criteria:** Conditional T&Cs, signature workflow, document management
- **Story Points:** 21

### Supply-Only Restrictions
**Priority: Medium**

**PO-048** As a **Purchaser**, I want standard P/Os limited to supply-only transactions so that installations, onsite work, and design services are properly handled through subcontracts.
- **Acceptance Criteria:** Transaction type validation, routing rules
- **Story Points:** 8

**PO-049** As a **PM**, I want clear distinction between P/Os and subcontracts so that different types of vendor relationships are managed appropriately.
- **Acceptance Criteria:** Clear UI distinction, workflow separation
- **Story Points:** 5

## Epic 9: P/O Numbering and Identification

### P/O Number Format
**Priority: High**

**PO-050** As a **Purchaser**, I want P/Os automatically numbered in YYJJJ-PO-XYZ format (e.g., 25301-PO-023) so that P/Os are clearly identified by year, job, and sequence.
- **Acceptance Criteria:** Automatic numbering algorithm, format validation
- **Story Points:** 13

**PO-051** As an **AP team member**, I want P/O numbers that clearly indicate the associated project so that I can easily match invoices to the correct job.
- **Acceptance Criteria:** Project identification in P/O number, search functionality
- **Story Points:** 5

**PO-052** As a **PM**, I want P/O numbers that include my project identifier so that I can easily track all purchasing activity for my jobs.
- **Acceptance Criteria:** Project-based filtering, reporting capabilities
- **Story Points:** 8

## Epic 10: P/O Lifecycle Management

### P/O Modification and Cancellation
**Priority: High**

**PO-063** As a **Purchaser**, I want to cancel a submitted P/O before it's issued to the vendor so that I can correct errors or handle changed requirements.
- **Acceptance Criteria:** Cancellation workflow, status validation, audit trail, vendor notification prevention
- **Story Points:** 13

**PO-064** As a **Purchaser**, I want to amend P/O details only while in draft status so that I can make corrections before final submission.
- **Acceptance Criteria:** Draft status validation, edit restrictions, version control
- **Story Points:** 8

**PO-065** As a **System**, I want to prevent P/O cancellation or amendments after invoicing has occurred so that financial integrity is maintained.
- **Acceptance Criteria:** Invoice status checking, hard blocks on modifications, error messages
- **Story Points:** 8

### Post-Submission Documentation
**Priority: Medium**

**PO-066** As a **Purchaser**, I want to add load slips and delivery notes after P/O submission so that I can document delivery details without modifying the original P/O.
- **Acceptance Criteria:** Document attachment post-submission, original P/O protection, timestamp tracking
- **Story Points:** 13

**PO-067** As a **Purchaser**, I want to append notes for vendor updates like backorders or staggered delivery so that all stakeholders are informed of changes.
- **Acceptance Criteria:** Note appending functionality, notification system, read-only original content
- **Story Points:** 8

**PO-068** As a **System**, I want to lock original P/O details (line items, totals) after submission so that the core purchase information remains tamper-proof.
- **Acceptance Criteria:** Field locking mechanism, edit prevention, audit logging
- **Story Points:** 8

## Epic 11: Mobile and Field Support

### Mobile Interface
**Priority: Medium**

**PO-053** As a **Foreman**, I want to create P/Os on my mobile device in the field so that I can purchase materials without returning to the office.
- **Acceptance Criteria:** Responsive mobile design, offline capability, sync functionality
- **Story Points:** 34

**PO-054** As a **Purchaser**, I want to take photos of receipts and quotes directly in the app so that I can quickly document purchases while on-site.
- **Acceptance Criteria:** Camera integration, image processing, cloud storage
- **Story Points:** 21

**PO-055** As a **Field user**, I want the app to work offline and sync when connectivity is restored so that I can work in areas with poor network coverage.
- **Acceptance Criteria:** Offline data storage, sync mechanism, conflict resolution
- **Story Points:** 34

## Epic 12: Reporting and Analytics

### Management Reporting
**Priority: Low**

**PO-056** As a **PM**, I want to generate reports on purchasing activity by project, vendor, and time period so that I can analyze spending patterns and vendor performance.
- **Acceptance Criteria:** Report builder, multiple output formats, scheduling
- **Story Points:** 21

**PO-057** As an **Executive**, I want to see company-wide purchasing metrics and trends so that I can make informed business decisions.
- **Acceptance Criteria:** Executive dashboard, KPI tracking, trend analysis
- **Story Points:** 34

**PO-058** As an **AP team member**, I want to track invoice processing times and identify bottlenecks so that I can improve payment efficiency.
- **Acceptance Criteria:** Process analytics, bottleneck identification, performance metrics
- **Story Points:** 21

## Epic 13: Technical Debt and Infrastructure

### System Performance
**Priority: Medium**

**PO-059** As a **System Administrator**, I want the system to handle concurrent users without performance degradation so that all users can work efficiently.
- **Acceptance Criteria:** Load testing, performance benchmarks, scalability planning
- **Story Points:** 21

**PO-060** As a **User**, I want the system to respond quickly to all actions so that my workflow is not interrupted.
- **Acceptance Criteria:** Response time targets, performance monitoring
- **Story Points:** 13

### Security and Compliance
**Priority: High**

**PO-061** As a **System Administrator**, I want all user actions logged for audit purposes so that we can track changes and maintain compliance.
- **Acceptance Criteria:** Comprehensive audit logging, tamper-proof records
- **Story Points:** 13

**PO-062** As a **User**, I want my data protected with appropriate security measures so that sensitive business information remains confidential.
- **Acceptance Criteria:** Data encryption, access controls, security testing
- **Story Points:** 21

## Backlog Summary

**Total Story Points:** 1,127
**High Priority Items:** 31 stories (646 points)
**Medium Priority Items:** 21 stories (362 points)
**Low Priority Items:** 7 stories (140 points)

## Release Planning Recommendations

### Phase 1 (MVP) - High Priority Core Features
- Standard P/O creation and management
- Basic approval workflows
- P/O numbering and identification
- Core authorization controls
- Essential integrations (JobCost, Timberline)

### Phase 2 - Enhanced Workflows
- Rolling P/O management
- Walk-up P/O workflow
- Quote integration
- Mobile support

### Phase 3 - Advanced Features
- Advanced reporting and analytics
- Enhanced document management
- Performance optimizations
- Additional integrations
