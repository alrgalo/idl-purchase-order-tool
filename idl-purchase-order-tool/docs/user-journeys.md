# IDL Purchase Order Tool - User Journeys

## Overview

This document outlines the key user journeys and flows within the IDL Purchase Order Tool, providing detailed step-by-step processes for primary user interactions. Each journey includes decision points, error handling, and role-specific variations to ensure comprehensive coverage of user experiences.

## Journey Categories

1. **Onboarding & Authentication**
2. **Standard P/O Creation**
3. **Rolling P/O Management**
4. **Walk-Up P/O Documentation**
5. **P/O Approval Workflows**
6. **P/O Management & Tracking**
7. **Error Recovery & Edge Cases**

---

## 1. Onboarding & Authentication

### 1.1 First-Time User Login

**Primary Actor:** New User (Any Role)  
**Trigger:** User receives login credentials from administrator  
**Goal:** Successfully access the system and understand basic navigation  

#### Journey Steps:

1. **Initial Access**
   - User navigates to application URL
   - System displays login page with IDL branding
   - User enters email and password credentials

2. **Authentication**
   - System validates credentials against user database
   - If successful: Generate JWT tokens and redirect to dashboard
   - If failed: Display error message and allow retry

3. **First-Time Dashboard Experience**
   - System displays welcome message with user name and role
   - Show authorization limit prominently
   - Display role-appropriate quick actions
   - Highlight key navigation elements

4. **Implicit Onboarding**
   - Dashboard cards provide clear action descriptions
   - Navigation menu shows role-based options
   - Tooltips and help text guide initial interactions

#### Success Criteria:
- User successfully logs in within 3 attempts
- User understands their role and authorization limit
- User can identify primary actions available to them

#### Error Scenarios:
- **Invalid credentials**: Clear error message, password reset option
- **Account locked**: Contact administrator message
- **Network issues**: Offline indicator, retry mechanism

---

### 1.2 Role Recognition & Permission Understanding

**Primary Actor:** Any User  
**Trigger:** User logs in and views dashboard  
**Goal:** User understands their permissions and limitations  

#### Journey Steps:

1. **Role Display**
   - Dashboard header shows user name and role label
   - Authorization limit displayed prominently
   - Role-specific color coding or badges

2. **Permission Discovery**
   - Available actions clearly visible in quick action cards
   - Disabled/hidden options for insufficient permissions
   - Contextual help explaining role limitations

3. **Limit Awareness**
   - Authorization limit shown in currency format
   - Visual indicators when approaching limits
   - Clear messaging about escalation procedures

---

## 2. Standard P/O Creation

### 2.1 Foreman Creating Material P/O (Under $1,000)

**Primary Actor:** Mike Thompson (Foreman)  
**Trigger:** Needs materials for active project  
**Goal:** Create and submit P/O within authorization limit  

#### Journey Steps:

1. **Initiation**
   - Login to dashboard
   - Click "Create P/O" quick action
   - System displays P/O type selection

2. **P/O Type Selection**
   - Select "Standard P/O" option
   - System shows authorization limit reminder ($1,000)
   - Click "Continue" to proceed

3. **Project & Cost Code Selection**
   - Select from dropdown of active projects assigned to user
   - System loads active cost codes for selected project
   - Choose appropriate cost code with descriptions
   - System validates user has access to selected project/cost code

4. **Vendor Information**
   - Enter vendor name (with autocomplete from previous vendors)
   - Add vendor contact details (name, phone, email)
   - Include delivery address (defaults to project location)
   - Set expected delivery date

5. **Line Item Entry**
   - Add item description (required)
   - Enter quantity and unit of measure
   - Input unit price
   - Select category (LA, EQ, MA, SC)
   - Choose tax code (GST2, PST7, GP, etc.)
   - System calculates line total automatically

6. **Multiple Line Items**
   - Use "Add Another Item" to include additional materials
   - "Apply to All" function for cost codes across line items
   - Running total displayed and updated in real-time

7. **Notes & Documentation**
   - Add vendor notes (delivery instructions, site contact)
   - Include internal notes for AP or PM reference
   - Attach supporting documents if needed

8. **Review & Validation**
   - System validates all mandatory fields completed
   - Check total amount against authorization limit
   - Display P/O summary for final review
   - Show calculated taxes and final total

9. **Submission**
   - Click "Submit P/O" button
   - System generates P/O number
   - P/O automatically sent to vendor via email
   - Copy sent to AP team
   - Confirmation message displayed with P/O number

#### Success Criteria:
- P/O created within 5 minutes
- All mandatory fields completed
- Total within authorization limit
- Vendor and AP receive P/O automatically

#### Error Scenarios:
- **Over limit**: Warning message, option to request supervisor override
- **Missing fields**: Field-specific error messages, form validation
- **Invalid cost code**: Error message, refresh cost code list
- **Network failure**: Save as draft, sync when connection restored

---

### 2.2 Super Creating Equipment P/O (Under $10,000)

**Primary Actor:** Sarah Martinez (Superintendent)  
**Trigger:** Equipment needed for multiple projects  
**Goal:** Create P/O with proper approvals and documentation  

#### Journey Steps:

1. **Enhanced Project Selection**
   - Dashboard shows multiple active projects
   - Select primary project for P/O
   - Option to split costs across multiple projects (if needed)

2. **Vendor Management**
   - Access to preferred vendor list
   - Vendor performance ratings and history
   - Option to request quotes from multiple vendors

3. **Advanced Line Item Features**
   - Bulk import from previous P/Os
   - Equipment specifications and model numbers
   - Rental vs. purchase options
   - Delivery scheduling for multiple locations

4. **Approval Workflow**
   - System determines if PM approval required (based on amount)
   - If over PM limit: Route to PM for approval before issuance
   - If within limit: Direct issuance with PM notification

#### Success Criteria:
- Efficient P/O creation with advanced features
- Proper routing based on approval limits
- Comprehensive documentation for equipment purchases

---

## 3. Rolling P/O Management

### 3.1 Creating Rolling P/O for Ongoing Supplies

**Primary Actor:** PM or Super  
**Trigger:** Need for recurring material deliveries  
**Goal:** Establish rolling P/O with spending ceiling  

#### Journey Steps:

1. **Rolling P/O Setup**
   - Select "Rolling P/O" type
   - Set total ceiling amount (pre-approved budget)
   - Define time period (monthly, quarterly)
   - Specify automatic renewal criteria

2. **Vendor Agreement**
   - Select approved vendor for rolling arrangement
   - Define delivery schedule and minimums
   - Set pricing terms and escalation clauses
   - Include performance metrics and KPIs

3. **Transaction Management**
   - Each delivery creates transaction against ceiling
   - Real-time balance tracking
   - Automatic alerts at 75% and 90% of ceiling
   - Monthly reconciliation and reporting

#### Success Criteria:
- Rolling P/O established with clear terms
- Automated tracking and alerts functional
- Vendor understands delivery and invoicing process

---

## 4. Walk-Up P/O Documentation

### 4.1 Foreman Documenting Emergency Purchase

**Primary Actor:** Mike Thompson (Foreman)  
**Trigger:** Made emergency purchase at hardware store  
**Goal:** Document purchase for AP processing and cost coding  

#### Journey Steps:

1. **Walk-Up P/O Initiation**
   - Select "Walk-Up P/O" from dashboard
   - System displays warning about post-purchase documentation
   - Acknowledge understanding of process

2. **Purchase Documentation**
   - Enter vendor information (hardware store details)
   - Input total amount spent
   - Upload receipt photo using device camera
   - Add context notes explaining emergency nature

3. **Line Item Coding**
   - Break down receipt into individual line items
   - Assign cost codes to each item
   - Select appropriate categories (MA, EQ, etc.)
   - System validates cost coding completeness

4. **Approval & Processing**
   - Submit for supervisor review
   - System locks user from creating new P/Os until closure
   - Supervisor approves or requests corrections
   - AP processes for payment upon approval

#### Success Criteria:
- Complete documentation within 24 hours of purchase
- All line items properly coded
- Receipt image clear and legible
- Supervisor approval obtained

#### Error Scenarios:
- **Missing receipt**: Request vendor duplicate, explain in notes
- **Unclear line items**: Contact vendor for detailed breakdown
- **Over authorization**: Automatic escalation to PM for approval

---

## 5. P/O Approval Workflows

### 5.1 PM Approving High-Value P/O

**Primary Actor:** David Chen (Project Manager)  
**Trigger:** P/O over $10,000 requires PM approval  
**Goal:** Review and approve/reject P/O efficiently  

#### Journey Steps:

1. **Approval Notification**
   - Email notification of pending P/O approval
   - Dashboard shows pending approvals count
   - Click notification to access P/O details

2. **P/O Review**
   - View complete P/O details and line items
   - Check project budget impact and remaining funds
   - Review vendor selection and pricing
   - Validate cost code assignments

3. **Decision Making**
   - Approve: P/O issued immediately to vendor
   - Reject: Return to creator with specific feedback
   - Request Changes: Send back with modification requests

4. **Budget Impact**
   - System updates project budget commitments
   - Alerts if P/O will exceed project budget
   - Integration with JobCost for budget tracking

#### Success Criteria:
- P/O reviewed within 24 hours
- Clear approval/rejection with reasoning
- Budget impacts properly tracked

---

## 6. P/O Management & Tracking

### 6.1 PM Monitoring Project P/O Activity

**Primary Actor:** David Chen (Project Manager)  
**Trigger:** Weekly project review process  
**Goal:** Monitor P/O status and project spending  

#### Journey Steps:

1. **P/O Dashboard Access**
   - Navigate to P/O list page
   - Filter by assigned projects
   - View status summary (draft, submitted, approved, invoiced)

2. **Status Monitoring**
   - Track P/O lifecycle from creation to payment
   - Identify bottlenecks in approval process
   - Monitor vendor performance and delivery

3. **Budget Analysis**
   - View spending by cost code and category
   - Compare actual vs. budgeted amounts
   - Identify trends and potential overruns

4. **Reporting**
   - Generate P/O reports for stakeholders
   - Export data for external analysis
   - Schedule automated reports

#### Success Criteria:
- Complete visibility into project P/O activity
- Proactive identification of budget issues
- Efficient reporting and communication

---

## 7. Error Recovery & Edge Cases

### 7.1 Network Connectivity Issues

**Primary Actor:** Any User  
**Trigger:** Network connection lost during P/O creation  
**Goal:** Preserve work and complete P/O when connection restored  

#### Journey Steps:

1. **Connection Loss Detection**
   - System detects network failure
   - Display offline indicator
   - Automatically save draft P/O locally

2. **Offline Mode**
   - Allow continued editing of draft P/O
   - Disable submission until connection restored
   - Show clear messaging about offline status

3. **Connection Restoration**
   - Detect network restoration
   - Sync draft P/O to server
   - Allow normal submission process

#### Success Criteria:
- No work lost due to connectivity issues
- Clear user communication about offline status
- Seamless transition back to online mode

---

### 7.2 Authorization Limit Exceeded

**Primary Actor:** Foreman or Super  
**Trigger:** P/O total exceeds user authorization limit  
**Goal:** Complete P/O with proper approvals  

#### Journey Steps:

1. **Limit Detection**
   - System calculates P/O total in real-time
   - Warning displayed when approaching limit
   - Error message when limit exceeded

2. **Override Options**
   - Request supervisor override for small overages
   - Split P/O into multiple smaller P/Os
   - Escalate to higher authorization level

3. **Approval Process**
   - Supervisor receives override request
   - Review P/O details and business justification
   - Approve override or request modifications

#### Success Criteria:
- Clear communication of limit issues
- Multiple resolution options available
- Efficient escalation process

---

## Journey Success Metrics

### Quantitative Metrics
- **P/O Creation Time**: Average time from initiation to submission
- **Error Rate**: Percentage of P/Os requiring corrections
- **Approval Time**: Average time for approval workflows
- **User Satisfaction**: Survey scores for ease of use

### Qualitative Metrics
- **User Confidence**: Ability to complete tasks without assistance
- **Error Recovery**: Success rate in resolving issues
- **Feature Adoption**: Usage of advanced features by role
- **Process Compliance**: Adherence to approval workflows

---

## Mobile-Specific Journey Considerations

### Camera Integration
- **Receipt Capture**: One-tap photo capture for walk-up P/Os
- **Document Scanning**: Multi-page document capture
- **Image Quality**: Automatic enhancement and validation

### Offline Capabilities
- **Draft Synchronization**: Seamless sync when connection restored
- **Data Persistence**: Local storage of critical information
- **Conflict Resolution**: Handling concurrent edits

### Touch Interactions
- **Form Navigation**: Optimized for touch input
- **Gesture Support**: Swipe actions for list management
- **Responsive Design**: Adaptive layouts for different screen sizes

---

## Accessibility Considerations

### Screen Reader Support
- **Semantic Markup**: Proper ARIA labels and roles
- **Navigation**: Logical tab order and focus management
- **Content Structure**: Clear headings and landmarks

### Keyboard Navigation
- **Shortcut Keys**: Common actions accessible via keyboard
- **Focus Indicators**: Clear visual focus states
- **Skip Links**: Bypass navigation for screen readers

### Visual Accessibility
- **Color Contrast**: WCAG AA compliance for all text
- **Font Sizing**: Scalable text for vision impairments
- **Color Independence**: Information not conveyed by color alone

---

This user journey documentation provides comprehensive coverage of the key user flows within the IDL Purchase Order Tool, ensuring that all user types can successfully complete their primary tasks while maintaining proper business processes and compliance requirements.
