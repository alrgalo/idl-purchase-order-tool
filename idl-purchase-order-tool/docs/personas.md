# IDL Purchase Order Tool - User Personas

## Primary Personas

### 1. Mike Thompson - Field Foreman
**Role:** Foreman  
**Authorization Limit:** $1,000  
**Experience:** 8 years in construction, 3 years with IDL Projects  

#### Background
Mike leads a crew of 6-8 workers on residential and small commercial projects. He's responsible for day-to-day operations and ensuring his team has the materials they need to keep projects moving. Mike is comfortable with basic technology but prefers simple, straightforward tools that don't slow him down.

#### Primary Goals
- Purchase materials quickly to avoid project delays
- Stay within his $1,000 authorization limit
- Ensure proper documentation for AP processing
- Maintain accurate cost coding for project budgets

#### Pain Points
- Often needs materials urgently but struggles with complex P/O processes
- Frequently forgets to include all required information, causing delays
- Has difficulty remembering correct cost codes for different projects
- Sometimes makes walk-up purchases at hardware stores without proper documentation

#### Key Actions in System
- Creates standard P/Os for materials under $1,000
- Uses walk-up P/Os for emergency hardware store purchases
- Uploads receipts and photos for documentation
- Selects from active cost codes for his assigned projects

#### Technology Comfort Level
- Basic smartphone and tablet user
- Prefers simple interfaces with clear instructions
- Needs prompts and guidance for mandatory fields

---

### 2. Sarah Martinez - Superintendent
**Role:** Super  
**Authorization Limit:** $10,000  
**Experience:** 12 years in construction, 5 years with IDL Projects  

#### Background
Sarah oversees multiple projects and crews, managing both operational needs and strategic purchasing decisions. She works closely with PMs and is responsible for ensuring efficient material flow across her projects. Sarah is tech-savvy and appreciates tools that help her manage complex workflows.

#### Primary Goals
- Efficiently manage purchasing across multiple active projects
- Ensure proper vendor relationships and pricing
- Maintain visibility into team purchasing activities
- Support Foremen while maintaining cost control

#### Pain Points
- Needs to track purchasing across multiple projects simultaneously
- Often receives incomplete P/O requests from Foremen that require follow-up
- Struggles to maintain vendor contact information accuracy
- Difficulty monitoring rolling P/Os and remaining balances

#### Key Actions in System
- Creates standard P/Os up to $10,000
- Reviews and approves Foreman P/O requests
- Manages rolling P/Os for ongoing items (rentals, fuel)
- Maintains vendor contact information and relationships

#### Technology Comfort Level
- Proficient with construction management software
- Comfortable with mobile apps and cloud-based tools
- Values efficiency features like auto-populate and bulk actions

---

### 3. David Chen - Project Manager
**Role:** PM  
**Authorization Limit:** $50,000  
**Experience:** 15 years in construction, 7 years with IDL Projects  

#### Background
David manages large commercial projects from initiation to completion. He's responsible for overall project budgets, vendor relationships, and ensuring all purchasing aligns with project specifications and timelines. David needs comprehensive visibility into all project costs and purchasing activities.

#### Primary Goals
- Maintain strict budget control across all project purchases
- Ensure proper cost coding and category allocation
- Monitor purchasing patterns and identify planning issues
- Approve high-value purchases with appropriate documentation

#### Pain Points
- Needs real-time visibility into project purchasing status
- Struggles with incomplete P/O information that delays approvals
- Difficulty tracking outstanding P/Os and pending invoices
- Challenges validating costs against quotes and budgets

#### Key Actions in System
- Creates and approves P/Os up to $50,000
- Reviews all P/Os before issuance for proper coding
- Manages P/O approval workflows and counter-signatures
- Monitors project purchasing statistics and KPIs
- Interfaces with JobCost system for budget management

#### Technology Comfort Level
- Advanced user of project management and accounting software
- Comfortable with complex dashboards and reporting tools
- Values integration with existing systems (JobCost, Timberline)

---

### 4. Jennifer Walsh - Accounts Payable Specialist
**Role:** AP Team Member  
**Authorization Limit:** N/A (Processing role)  
**Experience:** 6 years in accounting, 2 years with IDL Projects  

#### Background
Jennifer processes all incoming invoices and manages vendor payments. She needs complete P/O documentation to match invoices accurately and efficiently. Jennifer works closely with PMs during the approval process and maintains vendor payment relationships.

#### Primary Goals
- Efficiently match invoices to P/Os for payment processing
- Ensure all required documentation is complete before payment
- Minimize manual data entry and rework
- Maintain accurate vendor payment records

#### Pain Points
- Receives incomplete P/Os that require follow-up with purchasers
- Struggles to match invoices when P/O information is missing or incorrect
- Spends too much time manually coding invoices due to poor P/O data
- Difficulty tracking approval status and outstanding invoices

#### Key Actions in System
- Receives automatic copies of all issued P/Os
- Matches invoices to P/Os in the system
- Processes approved invoices for payment
- Tracks P/O status and outstanding approvals

#### Technology Comfort Level
- Proficient with accounting software and invoice processing systems
- Comfortable with document management and workflow tools
- Values automation and integration features

---

## Secondary Personas

### 5. Tom Rodriguez - Equipment Operator/Purchaser
**Role:** Specialized Purchaser  
**Authorization Limit:** Varies by assignment  
**Experience:** 10 years in heavy equipment, 4 years with IDL Projects  

#### Background
Tom specializes in equipment-related purchases and maintenance. He often deals with OEM vendors and complex part numbers. His purchases range from small consumables to major equipment repairs.

#### Primary Goals
- Accurately specify equipment parts and services
- Maintain equipment vendor relationships
- Ensure proper documentation for warranty and maintenance records

#### Pain Points
- Complex part numbers and technical specifications are difficult to enter
- OEM vendors often use part numbers that don't clearly describe the item
- Needs to include detailed context for equipment-specific purchases

#### Key Actions in System
- Creates P/Os for equipment parts and services
- Uploads technical specifications and part diagrams
- Maintains detailed context notes for complex purchases

---

### 6. Lisa Park - Vendor Representative
**Role:** External Vendor  
**Authorization Limit:** N/A (Receiving role)  
**Experience:** 8 years in construction supply, works with multiple contractors  

#### Background
Lisa represents a major construction supply company that works with IDL Projects regularly. She needs clear P/O information to process orders accurately and ensure proper invoicing.

#### Primary Goals
- Receive complete P/O information for accurate order processing
- Maintain good relationships with IDL purchasing team
- Ensure invoices are sent to correct recipients for prompt payment

#### Pain Points
- Sometimes receives incomplete P/Os with missing contact information
- Unclear delivery instructions or site contact details
- Confusion about proper invoice recipients (AP vs. purchaser)

#### Key Actions in System
- Receives P/Os electronically with complete information
- Confirms orders and delivery details
- Sends invoices to AP with P/O reference information

---

## Persona Usage Guidelines

### For Development Teams
- **Mike (Foreman):** Focus on simplicity, mobile-first design, clear prompts
- **Sarah (Super):** Emphasize efficiency features, multi-project views, vendor management
- **David (PM):** Prioritize comprehensive dashboards, reporting, and approval workflows
- **Jennifer (AP):** Ensure seamless invoice matching, automation, and integration features

### For User Experience Design
- Design for the least tech-savvy persona (Mike) while providing advanced features for power users (David)
- Ensure mobile responsiveness for field users (Mike, Tom)
- Provide clear visual hierarchy and status indicators for all users
- Include contextual help and guidance throughout the workflow

### For Testing and Validation
- Test common workflows with each persona's typical use cases
- Validate that authorization limits and approval workflows function correctly
- Ensure integration points work seamlessly for PM and AP personas
- Verify mobile usability for field-based personas
