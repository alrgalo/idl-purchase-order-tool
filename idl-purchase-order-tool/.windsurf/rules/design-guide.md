---
trigger: always_on
---

# IDL Purchase Order Tool - Design Guide

## Overview

This design guide establishes consistent patterns, conventions, and behaviors for the IDL Purchase Order Tool. It ensures alignment between developers and designers on structure, interaction, and styling based on the system's business requirements and user needs.

## Typography & Fonts

### Primary Font: Inter
- **Usage:** Primary font for all interfaces
- **Weights:** 400 (Regular), 500 (Medium), 600 (SemiBold)
- **Import:** `@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap');`

### Typography Scale
```
h1: 32px, SemiBold (600)
h2: 28px, SemiBold (600)
h3: 24px, SemiBold (600)
h4: 24px, SemiBold (600), line-height: 1.3
h5: 20px, SemiBold (600)
h6: 18px, SemiBold (600)
subtitle1: 14px, Regular (400), color: rgba(0, 0, 0, 0.6)
body1: 14px, Regular (400)
body2: 12px, Regular (400)
```

## Form Design Standards

### Layout & Structure
- **Form Width:** Maximum 448px (maxWidth="xs" in Material-UI)
- **Single Column:** All forms follow a vertical, single-column layout
- **Field Groups:** Related fields (e.g., First Name + Last Name) use horizontal layout with equal widths

### Spacing Standards
- **Form Container:**
  - Top/bottom margin: 32px on desktop, 24px on mobile
  - Padding: 24px on desktop, 16px on mobile

- **Form Elements:**
  - Between unrelated fields: 16px (Stack spacing={2})
  - Between related fields (e.g., password + confirm): 12px (Stack spacing={1.5})
  - Between field groups (e.g., name fields + email): 20px (Stack spacing={2.5})
  - Between sections (e.g., inputs + terms): 24px (Stack spacing={3})
  - Between section and primary action: 24px

- **Typography:**
  - Title to subtitle: 8px
  - Subtitle to first field: 24px
  - Label to input: 6px
  - Input to helper text: 4px

- **Actions & Links:**
  - Between secondary actions: 16px
  - After primary button to footer text: 16px

### Form Fields
- **Input Height:** 48px minimum for touch targets
- **Label Style:** 14px, Regular weight
- **Input Text:** 14px, Regular weight
- **Helper Text:** 12px, placed directly below input
- **Error Messages:** 12px, error color, no left margin
- **Padding:** 12px 16px for input fields

### Interactive Elements
- **Buttons:**
  - Height: 48px minimum
  - Text: 14px, no text transform
  - Padding: 12px 24px
  - Full width in forms
  - Primary action at bottom of form

- **Checkboxes & Radio:**
  - Label: 14px body text
  - Touch target: 44px minimum
  - 9px internal padding

- **Links:**
  - No text decoration by default
  - Underline on hover
  - Consistent with body text size
  - Primary color

### Form Sections
- **Headers:**
  - Title: 24px (h4), SemiBold, centered
  - Subtitle: 14px (subtitle1), centered, secondary text
  - Bottom margin: 32px from subtitle to form

- **Field Groups:**
  ```jsx
  <Box sx={{ display: 'flex', gap: 2 }}>
    <FormField name="firstName" />
    <FormField name="lastName" />
  </Box>
  ```

### Validation & Feedback
- **Error States:**
  - Red border on error
  - Error message below input
  - Icon indicators where applicable
  - No error until first interaction

- **Success States:**
  - Green checkmark for validated fields
  - Success messages use success color
  - Subtle success indicators

## Design Principles

### 1. Accessibility-First Design
- **Universal Access:** Design for users with diverse abilities and assistive technologies
- **WCAG 2.1 AA Compliance:** Meet or exceed Web Content Accessibility Guidelines
- **Inclusive Design:** Consider cognitive, motor, visual, and auditory accessibility needs
- **Progressive Enhancement:** Core functionality works without JavaScript or advanced features

### 2. Role-Based Design
- **Adaptive Interfaces:** UI adapts based on user role (Foreman, Super, PM, Admin)
- **Progressive Disclosure:** Show relevant features based on authorization levels
- **Context-Aware Actions:** Available actions change based on user permissions and P/O status
- **Clear Role Indicators:** Always visible role badges and authorization limits

### 3. Mobile-First Approach
- **Field-Optimized:** Primary design for mobile/tablet use by field personnel
- **Touch-Friendly:** Minimum 44px touch targets (48px preferred), appropriate spacing
- **Offline Capability:** Graceful degradation when connectivity is poor
- **One-Handed Operation:** Critical actions accessible with thumb navigation

### 4. Process-Driven UX
- **Workflow Guidance:** Clear visual indicators of current step and next actions
- **Error Prevention:** Proactive validation and helpful error messages
- **Status Transparency:** Always show current P/O status and available actions
- **Cognitive Load Reduction:** Minimize mental effort required for task completion

### 5. Performance-Conscious Design
- **Fast Loading:** Critical content loads within 2 seconds
- **Perceived Performance:** Use loading states and skeleton screens
- **Bandwidth Awareness:** Optimize for varying network conditions
- **Battery Efficiency:** Minimize resource-intensive animations and processes

## Naming Conventions

### System Terminology
**Consistent terminology across all interfaces:**

- **P/O** (not "Purchase Order" spelled out)
- **PM** (Project Manager)
- **Super** (Superintendent) 
- **AP** (Accounts Payable)
- **Purchaser** (generic term for any user creating P/Os)
- **Cost Code** (not "cost center" or "budget code")
- **JobCost** (external system name)
- **Timberline** (external system name)

### P/O Types
- **Standard P/O** - Regular supply-only purchases
- **Rolling P/O** - Ongoing purchases with ceiling amounts
- **Walk-Up P/O** - Emergency/field purchases requiring justification

### Status Labels
- **Draft** - P/O being created, not yet submitted
- **Submitted** - Awaiting approval
- **Approved** - Approved but not yet issued to vendor
- **Issued** - Sent to vendor
- **Invoiced** - Invoice received and matched
- **Cancelled** - P/O cancelled before completion
- **Closed** - P/O completed and archived

### Category Codes
- **LA** - Labour
- **EQ** - Equipment  
- **MA** - Material
- **SC** - Subcontractor

## UI Component Patterns

### 1. P/O Number Display
```
Format: YYJJJ-PO-XYZ
Example: 25301-PO-023

Visual Treatment:
- Monospace font for consistency
- Prominent display in headers
- Clickable for P/O details
- Color coding by status (draft=gray, approved=green, etc.)
```

### 2. Authorization Limit Indicators
```
Visual Hierarchy:
- Foreman: $1,000 (Blue badge)
- Super: $10,000 (Orange badge)  
- PM: $50,000 (Green badge)
- Over $50K: Red warning with escalation required
```

### 3. Status Indicators
```
Status Pills:
- Draft: Gray background, dark text
- Submitted: Yellow background, dark text
- Approved: Green background, white text
- Issued: Blue background, white text
- Invoiced: Purple background, white text
- Cancelled: Red background, white text
- Closed: Dark gray background, white text
```

### 4. Form Field Patterns

#### Required Fields
```html
<!-- Always mark required fields with asterisk -->
<label for="project-number">IDL Project Number *</label>
<input type="text" id="project-number" required aria-required="true">

<!-- Show field-level validation immediately -->
<div class="field-error" role="alert">
  Project number is required
</div>
```

#### Cost Code Selection
```html
<!-- Dropdown with search, filtered by active codes -->
<label for="cost-code">Cost Code *</label>
<select id="cost-code" required>
  <option value="">Select Cost Code</option>
  <optgroup label="Labour (LA)">
    <option value="LA-001">LA-001 - Site Preparation</option>
  </optgroup>
  <optgroup label="Equipment (EQ)">
    <option value="EQ-001">EQ-001 - Heavy Machinery</option>
  </optgroup>
</select>
```

#### Currency Fields
```html
<!-- Consistent currency formatting -->
<label for="unit-price">Unit Price *</label>
<div class="currency-input">
  <span class="currency-symbol">$</span>
  <input type="number" id="unit-price" step="0.01" min="0" placeholder="0.00">
</div>
```

### 5. Action Button Patterns

#### Primary Actions
```html
<!-- High-contrast, prominent placement -->
<button class="btn btn-primary" type="submit">
  Submit P/O
</button>
```

#### Secondary Actions
```html
<!-- Lower contrast, secondary placement -->
<button class="btn btn-secondary" type="button">
  Save as Draft
</button>
```

#### Destructive Actions
```html
<!-- Red styling with confirmation -->
<button class="btn btn-danger" onclick="confirmCancel()">
  Cancel P/O
</button>
```

#### Override Actions
```html
<!-- Special styling for supervisor override requests -->
<button class="btn btn-warning" type="button">
  Request Override
</button>
```

## Layout Patterns

### 1. P/O Creation Form Layout
```
┌─────────────────────────────────────────┐
│ Header: P/O Number + Status             │
├─────────────────────────────────────────┤
│ Project Information                     │
│ ├─ IDL Project Number *                 │
│ ├─ Project Location                     │
│ └─ Delivery Date                        │
├─────────────────────────────────────────┤
│ Vendor Information                      │
│ ├─ Vendor Name *                        │
│ ├─ Contact Email *                      │
│ └─ Contact Phone                        │
├─────────────────────────────────────────┤
│ Line Items                              │
│ ├─ Add Line Item Button                 │
│ └─ Line Item Rows                       │
│     ├─ Description *                    │
│     ├─ Quantity * | UoM *               │
│     ├─ Unit Price * | Line Total        │
│     ├─ Cost Code * | Category *         │
│     └─ Remove Button                    │
├─────────────────────────────────────────┤
│ Totals Section                          │
│ ├─ Subtotal                             │
│ ├─ Tax                                  │
│ └─ Total Amount                         │
├─────────────────────────────────────────┤
│ Notes Section                           │
│ ├─ Internal Notes                       │
│ └─ Vendor Notes                         │
├─────────────────────────────────────────┤
│ Actions                                 │
│ ├─ Save Draft | Submit P/O              │
│ └─ Cancel                               │
└─────────────────────────────────────────┘
```

### 2. Dashboard Layout
```
┌─────────────────────────────────────────┐
│ User Info + Role Badge                  │
├─────────────────────────────────────────┤
│ Quick Actions                           │
│ ├─ Create Standard P/O                  │
│ ├─ Create Rolling P/O                   │
│ └─ Create Walk-Up P/O                   │
├─────────────────────────────────────────┤
│ Pending Approvals (if applicable)       │
│ └─ List of P/Os awaiting approval       │
├─────────────────────────────────────────┤
│ Recent P/Os                             │
│ └─ Table/List of recent P/Os            │
├─────────────────────────────────────────┤
│ Statistics (PM view)                    │
│ ├─ Total P/O Value                      │
│ ├─ Number of P/Os                       │
│ └─ Average P/O Value                    │
└─────────────────────────────────────────┘
```

### 3. Mobile-Optimized Layout
```
┌─────────────────┐
│ Header          │
├─────────────────┤
│ Navigation      │
│ (Collapsible)   │
├─────────────────┤
│ Main Content    │
│ (Single Column) │
│                 │
│ Form Fields     │
│ (Full Width)    │
│                 │
│ Actions         │
│ (Stacked)       │
├─────────────────┤
│ Footer          │
└─────────────────┘
```

## Interaction Patterns

### 1. Form Validation
```javascript
// Progressive validation pattern
const validationRules = {
  immediate: ['required', 'format'], // Validate on blur
  deferred: ['business_rules'],      // Validate on submit
  server: ['uniqueness', 'limits']   // Validate server-side
};

// Error display pattern
function showFieldError(fieldId, message) {
  // Remove existing error
  clearFieldError(fieldId);
  
  // Add error styling
  field.classList.add('field-error');
  
  // Show error message
  const errorDiv = document.createElement('div');
  errorDiv.className = 'error-message';
  errorDiv.setAttribute('role', 'alert');
  errorDiv.textContent = message;
  
  field.parentNode.appendChild(errorDiv);
}
```

### 2. Authorization Limit Handling
```javascript
// Check authorization limit pattern
function checkAuthorizationLimit(amount, userRole) {
  const limits = {
    'foreman': 1000,
    'super': 10000,
    'pm': 50000
  };
  
  const userLimit = limits[userRole];
  
  if (amount > userLimit) {
    return {
      allowed: false,
      requiresOverride: true,
      message: `Amount $${amount} exceeds your limit of $${userLimit}. Request supervisor override?`
    };
  }
  
  return { allowed: true };
}
```

### 3. P/O Status Workflow
```javascript
// Status transition pattern
const statusTransitions = {
  'draft': ['submitted', 'cancelled'],
  'submitted': ['approved', 'rejected', 'cancelled'],
  'approved': ['issued', 'cancelled'],
  'issued': ['invoiced', 'cancelled'],
  'invoiced': ['closed'],
  'cancelled': [], // Terminal state
  'closed': []     // Terminal state
};

function canTransitionTo(currentStatus, newStatus) {
  return statusTransitions[currentStatus]?.includes(newStatus) || false;
}
```

### 4. Document Upload Pattern
```javascript
// File upload with validation
function handleFileUpload(file, poId, documentType) {
  // Validate file type and size
  const allowedTypes = ['image/jpeg', 'image/png', 'application/pdf'];
  const maxSize = 10 * 1024 * 1024; // 10MB
  
  if (!allowedTypes.includes(file.type)) {
    showError('Please upload JPG, PNG, or PDF files only');
    return;
  }
  
  if (file.size > maxSize) {
    showError('File size must be less than 10MB');
    return;
  }
  
  // Show upload progress
  showUploadProgress();
  
  // Upload file
  uploadFile(file, poId, documentType)
    .then(response => {
      hideUploadProgress();
      showSuccess('Document uploaded successfully');
      refreshDocumentList();
    })
    .catch(error => {
      hideUploadProgress();
      showError('Upload failed: ' + error.message);
    });
}
```

## Visual Design System

### 1. Color Palette (WCAG AA Compliant)
```css
/* Primary Colors - All meet 4.5:1 contrast ratio on white backgrounds */
:root {
  --primary-blue: #1565c0;        /* 7.0:1 contrast ratio */
  --primary-blue-light: #1976d2;  /* 5.9:1 contrast ratio */
  --primary-blue-dark: #0d47a1;   /* 10.7:1 contrast ratio */
  --primary-blue-hover: #1976d2;  /* Hover state */
  --primary-blue-focus: #0d47a1;  /* Focus state */
  
  /* Status Colors - Enhanced for accessibility */
  --status-draft: #616161;        /* 7.0:1 contrast - Gray 700 */
  --status-submitted: #ef6c00;    /* 4.6:1 contrast - Orange 800 */
  --status-approved: #2e7d32;     /* 5.4:1 contrast - Green 800 */
  --status-issued: #1565c0;       /* 7.0:1 contrast - Blue 800 */
  --status-invoiced: #6a1b9a;     /* 6.3:1 contrast - Purple 800 */
  --status-cancelled: #c62828;    /* 5.5:1 contrast - Red 800 */
  --status-closed: #424242;       /* 9.6:1 contrast - Gray 800 */
  
  /* Status Background Colors - Light variants for pills */
  --status-draft-bg: #f5f5f5;
  --status-submitted-bg: #fff3e0;
  --status-approved-bg: #e8f5e8;
  --status-issued-bg: #e3f2fd;
  --status-invoiced-bg: #f3e5f5;
  --status-cancelled-bg: #ffebee;
  --status-closed-bg: #fafafa;
  
  /* Role Colors - WCAG AA compliant */
  --role-foreman: #1565c0;        /* Blue 800 - 7.0:1 contrast */
  --role-super: #ef6c00;          /* Orange 800 - 4.6:1 contrast */
  --role-pm: #2e7d32;             /* Green 800 - 5.4:1 contrast */
  --role-admin: #6a1b9a;          /* Purple 800 - 6.3:1 contrast */
  
  /* Role Background Colors */
  --role-foreman-bg: #e3f2fd;
  --role-super-bg: #fff3e0;
  --role-pm-bg: #e8f5e8;
  --role-admin-bg: #f3e5f5;
  
  /* Semantic Colors - Enhanced accessibility */
  --success: #2e7d32;             /* Green 800 - 5.4:1 contrast */
  --success-light: #4caf50;       /* Green 500 - 3.4:1 contrast (large text only) */
  --success-bg: #e8f5e8;          /* Light green background */
  
  --warning: #ef6c00;             /* Orange 800 - 4.6:1 contrast */
  --warning-light: #ff9800;       /* Orange 500 - 2.9:1 contrast (large text only) */
  --warning-bg: #fff3e0;          /* Light orange background */
  
  --error: #c62828;               /* Red 800 - 5.5:1 contrast */
  --error-light: #f44336;         /* Red 500 - 3.8:1 contrast (large text only) */
  --error-bg: #ffebee;            /* Light red background */
  
  --info: #1565c0;                /* Blue 800 - 7.0:1 contrast */
  --info-light: #2196f3;          /* Blue 500 - 3.1:1 contrast (large text only) */
  --info-bg: #e3f2fd;             /* Light blue background */
  
  /* Neutral Colors - Full accessibility range */
  --white: #ffffff;               /* Pure white */
  --gray-50: #fafafa;             /* Lightest gray */
  --gray-100: #f5f5f5;            /* Very light gray */
  --gray-200: #eeeeee;            /* Light gray */
  --gray-300: #e0e0e0;            /* Medium light gray */
  --gray-400: #bdbdbd;            /* Medium gray - 2.8:1 contrast (decorative only) */
  --gray-500: #9e9e9e;            /* Medium gray - 2.0:1 contrast (decorative only) */
  --gray-600: #757575;            /* Dark medium gray - 4.5:1 contrast */
  --gray-700: #616161;            /* Dark gray - 7.0:1 contrast */
  --gray-800: #424242;            /* Very dark gray - 9.6:1 contrast */
  --gray-900: #212121;            /* Darkest gray - 16.1:1 contrast */
  --black: #000000;               /* Pure black */
  
  /* Focus and Interaction States */
  --focus-ring: #1976d2;          /* Focus ring color */
  --focus-ring-offset: 2px;       /* Focus ring offset */
  --hover-overlay: rgba(0, 0, 0, 0.04); /* Subtle hover overlay */
  --active-overlay: rgba(0, 0, 0, 0.08); /* Active state overlay */
  
  /* High Contrast Mode Support */
  --border-contrast: #757575;     /* Visible in high contrast mode */
  --text-contrast: #212121;       /* Maximum text contrast */
}

/* High Contrast Mode Overrides */
@media (prefers-contrast: high) {
  :root {
    --primary-blue: #0d47a1;
    --gray-600: #424242;
    --gray-700: #212121;
  }
}

/* Reduced Motion Support */
@media (prefers-reduced-motion: reduce) {
  :root {
    --transition-duration: 0s;
    --animation-duration: 0s;
  }
}
```

### 2. Typography
```css
/* Font Stack */
body {
  font-family: 'Roboto', 'Helvetica Neue', Arial, sans-serif;
  font-size: 16px;
  line-height: 1.5;
  color: var(--gray-800);
}

/* Headings */
h1 { font-size: 2rem; font-weight: 500; margin-bottom: 1rem; }
h2 { font-size: 1.5rem; font-weight: 500; margin-bottom: 0.875rem; }
h3 { font-size: 1.25rem; font-weight: 500; margin-bottom: 0.75rem; }

/* P/O Numbers - Monospace */
.po-number {
  font-family: 'Roboto Mono', 'Courier New', monospace;
  font-weight: 500;
  letter-spacing: 0.5px;
}

/* Form Labels */
label {
  font-weight: 500;
  color: var(--gray-700);
  margin-bottom: 0.25rem;
  display: block;
}

/* Required Field Indicator */
.required::after {
  content: ' *';
  color: var(--error);
}
```

### 3. Spacing System
```css
/* Spacing Scale (8px base unit) */
:root {
  --space-xs: 0.25rem;  /* 4px */
  --space-sm: 0.5rem;   /* 8px */
  --space-md: 1rem;     /* 16px */
  --space-lg: 1.5rem;   /* 24px */
  --space-xl: 2rem;     /* 32px */
  --space-2xl: 3rem;    /* 48px */
  --space-3xl: 4rem;    /* 64px */
}

/* Component Spacing */
.form-group {
  margin-bottom: var(--space-lg);
}

.form-row {
  margin-bottom: var(--space-md);
}

.button-group > * {
  margin-right: var(--space-sm);
}

.button-group > *:last-child {
  margin-right: 0;
}
```

### 4. Component Styles
```css
/* Buttons */
.btn {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 4px;
  font-weight: 500;
  text-decoration: none;
  display: inline-block;
  cursor: pointer;
  transition: all 0.2s ease;
  min-height: 44px; /* Touch-friendly */
}

.btn-primary {
  background-color: var(--primary-blue);
  color: white;
}

.btn-primary:hover {
  background-color: var(--primary-blue-dark);
}

.btn-secondary {
  background-color: var(--gray-200);
  color: var(--gray-800);
}

.btn-danger {
  background-color: var(--error);
  color: white;
}

.btn-warning {
  background-color: var(--warning);
  color: white;
}

/* Form Controls */
.form-control {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid var(--gray-300);
  border-radius: 4px;
  font-size: 1rem;
  transition: border-color 0.2s ease;
}

.form-control:focus {
  outline: none;
  border-color: var(--primary-blue);
  box-shadow: 0 0 0 2px rgba(25, 118, 210, 0.2);
}

.form-control.error {
  border-color: var(--error);
}

/* Status Pills */
.status-pill {
  display: inline-block;
  padding: 0.25rem 0.75rem;
  border-radius: 12px;
  font-size: 0.875rem;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.status-draft { background-color: var(--status-draft); color: white; }
.status-submitted { background-color: var(--status-submitted); color: white; }
.status-approved { background-color: var(--status-approved); color: white; }
.status-issued { background-color: var(--status-issued); color: white; }
.status-invoiced { background-color: var(--status-invoiced); color: white; }
.status-cancelled { background-color: var(--status-cancelled); color: white; }
.status-closed { background-color: var(--status-closed); color: white; }

/* Role Badges */
.role-badge {
  display: inline-block;
  padding: 0.25rem 0.5rem;
  border-radius: 4px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.role-foreman { background-color: var(--role-foreman); color: white; }
.role-super { background-color: var(--role-super); color: white; }
.role-pm { background-color: var(--role-pm); color: white; }
.role-admin { background-color: var(--role-admin); color: white; }
```

## Responsive Design Patterns

### 1. Breakpoints
```css
/* Mobile First Breakpoints */
:root {
  --breakpoint-sm: 576px;  /* Small devices */
  --breakpoint-md: 768px;  /* Tablets */
  --breakpoint-lg: 992px;  /* Desktops */
  --breakpoint-xl: 1200px; /* Large desktops */
}

/* Media Query Mixins */
@media (min-width: 768px) {
  .form-row {
    display: flex;
    gap: var(--space-md);
  }
  
  .form-col {
    flex: 1;
  }
}
```

### 2. Mobile Optimizations
```css
/* Touch-friendly sizing */
@media (max-width: 767px) {
  .btn {
    min-height: 48px;
    font-size: 1.1rem;
  }
  
  .form-control {
    min-height: 48px;
    font-size: 16px; /* Prevents zoom on iOS */
  }
  
  /* Stack form elements */
  .form-row {
    flex-direction: column;
  }
  
  /* Full-width buttons on mobile */
  .btn-mobile-full {
    width: 100%;
    margin-bottom: var(--space-sm);
  }
}
```

## Accessibility Guidelines (WCAG 2.1 AA Compliant)

### 1. Semantic HTML Structure
```html
<!-- Use proper semantic elements -->
<main id="main-content">
  <header>
    <h1>Purchase Order Creation</h1>
    <nav aria-label="Breadcrumb">
      <ol>
        <li><a href="/dashboard">Dashboard</a></li>
        <li aria-current="page">Create P/O</li>
      </ol>
    </nav>
  </header>
  
  <section aria-labelledby="project-info-heading">
    <h2 id="project-info-heading">Project Information</h2>
    <form novalidate> <!-- Use custom validation for better UX -->
      <fieldset>
        <legend>Required Project Details</legend>
        <!-- Form fields here -->
      </fieldset>
    </form>
  </section>
</main>

<!-- Landmark roles for screen readers -->
<nav role="navigation" aria-label="Main navigation">
<main role="main">
<aside role="complementary" aria-label="Quick actions">
<footer role="contentinfo">
```

### 2. Enhanced Keyboard Navigation
```html
<!-- Logical tab order (avoid tabindex > 0) -->
<form>
  <input type="text" id="project-number" required>
  <select id="cost-code" required>
  <button type="submit">Submit P/O</button>
</form>

<!-- Skip links - always first focusable element -->
<a href="#main-content" class="skip-link">Skip to main content</a>
<a href="#navigation" class="skip-link">Skip to navigation</a>
<a href="#search" class="skip-link">Skip to search</a>

<!-- Keyboard shortcuts with proper announcement -->
<button type="button" 
        accesskey="s" 
        aria-keyshortcuts="Alt+S"
        title="Save P/O (Alt+S)">
  Save P/O
</button>

<!-- Focus management for dynamic content -->
<script>
// Focus management after dynamic updates
function showSuccessMessage(message) {
  const alert = document.createElement('div');
  alert.setAttribute('role', 'alert');
  alert.setAttribute('aria-live', 'assertive');
  alert.setAttribute('tabindex', '-1');
  alert.textContent = message;
  document.body.appendChild(alert);
  alert.focus(); // Announce to screen readers
}
</script>
```

### 3. ARIA Labels, Roles, and Properties
```html
<!-- Comprehensive form labeling -->
<div class="form-group">
  <label for="amount" class="required">
    Amount
    <span class="required-indicator" aria-label="required">*</span>
  </label>
  <div class="input-group">
    <span class="input-group-text" aria-hidden="true">$</span>
    <input type="number" 
           id="amount" 
           name="amount"
           aria-required="true"
           aria-describedby="amount-help amount-error"
           aria-invalid="false"
           step="0.01"
           min="0">
  </div>
  <div id="amount-help" class="form-help">
    Enter the total amount for this line item
  </div>
  <div id="amount-error" class="error-message" role="alert" aria-live="polite">
    <!-- Error message appears here -->
  </div>
</div>

<!-- Status announcements with proper ARIA -->
<div id="status-announcements" 
     role="status" 
     aria-live="polite" 
     aria-atomic="true"
     class="sr-only">
  <!-- Status updates announced here -->
</div>

<!-- Complex widgets with full ARIA support -->
<div role="combobox" 
     aria-expanded="false" 
     aria-haspopup="listbox"
     aria-owns="vendor-listbox">
  <label for="vendor-search">Vendor Name</label>
  <input type="text" 
         id="vendor-search"
         aria-autocomplete="list"
         aria-describedby="vendor-help">
  <ul id="vendor-listbox" role="listbox" aria-hidden="true">
    <li role="option" aria-selected="false">ABC Supply Co.</li>
    <li role="option" aria-selected="false">XYZ Materials</li>
  </ul>
</div>

<!-- Data tables with proper headers -->
<table role="table" aria-label="Purchase order line items">
  <caption>Line items for P/O 25301-PO-023</caption>
  <thead>
    <tr>
      <th scope="col" id="description">Description</th>
      <th scope="col" id="quantity">Quantity</th>
      <th scope="col" id="unit-price">Unit Price</th>
      <th scope="col" id="total">Total</th>
      <th scope="col" id="actions">Actions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td headers="description">Construction Materials</td>
      <td headers="quantity">100</td>
      <td headers="unit-price">$25.00</td>
      <td headers="total">$2,500.00</td>
      <td headers="actions">
        <button type="button" aria-label="Edit Construction Materials line item">
          Edit
        </button>
      </td>
    </tr>
  </tbody>
</table>
```

### 4. Color and Contrast Standards
```css
/* WCAG AA Compliant Colors (4.5:1 minimum ratio) */
.error-message {
  color: var(--error);              /* #c62828 - 5.5:1 contrast */
  background-color: var(--error-bg); /* #ffebee - Light background */
  border-left: 4px solid var(--error);
}

.success-message {
  color: var(--success);              /* #2e7d32 - 5.4:1 contrast */
  background-color: var(--success-bg); /* #e8f5e8 - Light background */
}

.warning-message {
  color: var(--warning);              /* #ef6c00 - 4.6:1 contrast */
  background-color: var(--warning-bg); /* #fff3e0 - Light background */
}

/* Never rely solely on color - use multiple indicators */
.status-pill {
  position: relative;
}

.status-pill::before {
  content: '';
  display: inline-block;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  margin-right: 8px;
  background-color: currentColor;
}

/* High contrast mode support */
@media (prefers-contrast: high) {
  .form-control {
    border: 2px solid var(--border-contrast);
  }
  
  .btn {
    border: 2px solid currentColor;
  }
}

/* Focus indicators - always visible and high contrast */
.form-control:focus,
.btn:focus {
  outline: 3px solid var(--focus-ring);
  outline-offset: var(--focus-ring-offset);
  box-shadow: 0 0 0 1px var(--focus-ring);
}

/* Ensure focus is visible in high contrast mode */
@media (prefers-contrast: high) {
  *:focus {
    outline: 3px solid;
    outline-offset: 2px;
  }
}
```

### 5. Screen Reader Support
```html
<!-- Screen reader only content -->
<span class="sr-only">Current step: 2 of 4</span>

<!-- Descriptive link text -->
<a href="/po/25301-PO-023">
  View Purchase Order 25301-PO-023
  <span class="sr-only"> (opens in same window)</span>
</a>

<!-- Form instructions -->
<div id="form-instructions" class="sr-only">
  This form has 4 required fields. Required fields are marked with an asterisk.
  Form validation will occur as you complete each field.
</div>
<form aria-describedby="form-instructions">
  <!-- Form content -->
</form>

<!-- Loading states -->
<button type="submit" aria-describedby="submit-status">
  Submit P/O
</button>
<div id="submit-status" aria-live="polite" class="sr-only">
  <!-- "Submitting..." or "Submitted successfully" appears here -->
</div>

<!-- Complex interactions -->
<div role="dialog" 
     aria-labelledby="confirm-title" 
     aria-describedby="confirm-description"
     aria-modal="true">
  <h2 id="confirm-title">Confirm P/O Submission</h2>
  <p id="confirm-description">
    You are about to submit P/O 25301-PO-023 for $2,500.00. 
    This action cannot be undone.
  </p>
  <button type="button" autofocus>Confirm Submission</button>
  <button type="button">Cancel</button>
</div>
```

### 6. Mobile Accessibility
```css
/* Touch target sizing - minimum 44px, preferred 48px */
.btn,
.form-control,
.checkbox,
.radio {
  min-height: 48px;
  min-width: 48px;
}

/* Ensure adequate spacing between touch targets */
.btn + .btn {
  margin-left: 8px;
}

@media (max-width: 767px) {
  /* Larger touch targets on mobile */
  .btn {
    min-height: 52px;
    padding: 12px 16px;
  }
  
  .form-control {
    min-height: 52px;
    font-size: 16px; /* Prevents zoom on iOS */
  }
}
```

### 7. Motion and Animation Accessibility
```css
/* Respect user motion preferences */
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}

/* Safe animations that don't trigger vestibular disorders */
.fade-in {
  animation: fadeIn 0.3s ease-in-out;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Avoid parallax and auto-playing content */
.parallax {
  transform: none; /* Disable parallax for reduced motion */
}
```

### 8. Error Handling and Validation
```javascript
// Accessible form validation
function validateField(field) {
  const errorContainer = document.getElementById(field.id + '-error');
  const isValid = field.checkValidity();
  
  // Update ARIA attributes
  field.setAttribute('aria-invalid', !isValid);
  
  if (!isValid) {
    // Show error message
    errorContainer.textContent = getErrorMessage(field);
    errorContainer.style.display = 'block';
    
    // Announce error to screen readers
    announceToScreenReader(`Error in ${field.labels[0].textContent}: ${errorContainer.textContent}`);
  } else {
    // Clear error
    errorContainer.textContent = '';
    errorContainer.style.display = 'none';
  }
}

function announceToScreenReader(message) {
  const announcer = document.getElementById('status-announcements');
  announcer.textContent = message;
  
  // Clear after announcement
  setTimeout(() => {
    announcer.textContent = '';
  }, 1000);
}

// Focus management for single-page applications
function navigateToPage(pageTitle) {
  // Update page title
  document.title = `${pageTitle} - IDL Purchase Order Tool`;
  
  // Focus management
  const mainHeading = document.querySelector('h1');
  if (mainHeading) {
    mainHeading.setAttribute('tabindex', '-1');
    mainHeading.focus();
  }
  
  // Announce page change
  announceToScreenReader(`Navigated to ${pageTitle}`);
}
```

## UI/UX Best Practices

### 1. Information Architecture
```html
<!-- Clear content hierarchy -->
<main>
  <header>
    <h1>Purchase Order #25301-PO-023</h1>
    <div class="status-bar">
      <span class="status-pill status-draft">Draft</span>
      <span class="user-info">Created by John Smith (Foreman)</span>
      <time datetime="2025-01-15">January 15, 2025</time>
    </div>
  </header>
  
  <!-- Progressive disclosure of information -->
  <section class="summary-section">
    <h2>Summary</h2>
    <div class="key-metrics">
      <div class="metric">
        <span class="metric-label">Total Amount</span>
        <span class="metric-value">$2,500.00</span>
      </div>
      <div class="metric">
        <span class="metric-label">Line Items</span>
        <span class="metric-value">3</span>
      </div>
    </div>
  </section>
  
  <!-- Logical content grouping -->
  <section class="details-section">
    <h2>Details</h2>
    <details open>
      <summary>Project Information</summary>
      <!-- Project details -->
    </details>
    <details>
      <summary>Vendor Information</summary>
      <!-- Vendor details -->
    </details>
  </section>
</main>
```

### 2. Interaction Design Principles
```javascript
// Immediate feedback for user actions
function handleButtonClick(button, action) {
  // Visual feedback
  button.classList.add('loading');
  button.disabled = true;
  
  // Haptic feedback on mobile
  if ('vibrate' in navigator) {
    navigator.vibrate(50);
  }
  
  // Perform action with timeout protection
  const timeoutId = setTimeout(() => {
    showError('Action timed out. Please try again.');
    resetButton(button);
  }, 30000);
  
  action()
    .then(result => {
      clearTimeout(timeoutId);
      showSuccess('Action completed successfully');
      resetButton(button);
    })
    .catch(error => {
      clearTimeout(timeoutId);
      showError(error.message);
      resetButton(button);
    });
}

// Contextual help and guidance
function showContextualHelp(fieldId) {
  const helpContent = {
    'project-number': {
      title: 'IDL Project Number',
      content: 'Enter the 5-digit project number assigned to this job. You can find this on your project documentation or ask your supervisor.',
      example: 'Example: 12345'
    },
    'cost-code': {
      title: 'Cost Code Selection',
      content: 'Choose the appropriate cost code for this expense. Cost codes help track project expenses by category.',
      example: 'Example: LA-001 for Site Preparation'
    }
  };
  
  const help = helpContent[fieldId];
  if (help) {
    showTooltip(fieldId, help);
  }
}

// Smart defaults and auto-completion
function setupSmartDefaults() {
  // Remember user preferences
  const lastVendor = localStorage.getItem('lastUsedVendor');
  if (lastVendor) {
    document.getElementById('vendor-name').value = lastVendor;
  }
  
  // Auto-fill based on project context
  const projectNumber = document.getElementById('project-number').value;
  if (projectNumber) {
    fetchProjectDefaults(projectNumber).then(defaults => {
      if (defaults.deliveryAddress) {
        document.getElementById('delivery-address').value = defaults.deliveryAddress;
      }
    });
  }
}
```

### 3. Cognitive Load Reduction
```html
<!-- Chunking information into digestible sections -->
<div class="form-wizard">
  <nav class="wizard-nav" aria-label="Form progress">
    <ol class="progress-steps">
      <li class="step completed" aria-current="step">
        <span class="step-number">1</span>
        <span class="step-title">Project Info</span>
      </li>
      <li class="step current">
        <span class="step-number">2</span>
        <span class="step-title">Vendor Details</span>
      </li>
      <li class="step">
        <span class="step-number">3</span>
        <span class="step-title">Line Items</span>
      </li>
      <li class="step">
        <span class="step-number">4</span>
        <span class="step-title">Review</span>
      </li>
    </ol>
  </nav>
  
  <!-- Current step content -->
  <div class="wizard-content">
    <h2>Step 2: Vendor Details</h2>
    <p class="step-description">
      Enter the vendor information for this purchase order.
    </p>
    <!-- Form fields for current step -->
  </div>
  
  <!-- Navigation controls -->
  <div class="wizard-controls">
    <button type="button" class="btn btn-secondary">Previous</button>
    <button type="button" class="btn btn-primary">Next</button>
  </div>
</div>

<!-- Contextual information display -->
<aside class="context-panel" aria-label="Additional information">
  <div class="context-item">
    <h3>Your Authorization Limit</h3>
    <div class="limit-display">
      <span class="limit-amount">$1,000</span>
      <span class="limit-role">Foreman</span>
    </div>
    <p class="limit-help">
      P/Os over this amount require supervisor approval.
    </p>
  </div>
  
  <div class="context-item">
    <h3>Recent Vendors</h3>
    <ul class="recent-list">
      <li><button type="button" class="vendor-quick-select">ABC Supply Co.</button></li>
      <li><button type="button" class="vendor-quick-select">XYZ Materials</button></li>
    </ul>
  </div>
</aside>
```

### 4. Error Prevention Strategies
```javascript
// Proactive validation and guidance
function setupProactiveValidation() {
  const amountField = document.getElementById('amount');
  const userLimit = getCurrentUserLimit();
  
  amountField.addEventListener('input', function(e) {
    const value = parseFloat(e.target.value);
    
    if (value > userLimit) {
      showWarning(
        `Amount exceeds your $${userLimit} limit. ` +
        `Supervisor approval will be required.`,
        'warning'
      );
    } else {
      clearWarning();
    }
  });
}

// Confirmation for critical actions
function confirmCriticalAction(action, details) {
  return new Promise((resolve, reject) => {
    const modal = createConfirmationModal({
      title: `Confirm ${action}`,
      message: `Are you sure you want to ${action.toLowerCase()}?`,
      details: details,
      confirmText: action,
      cancelText: 'Cancel',
      type: 'warning'
    });
    
    modal.onConfirm = () => resolve(true);
    modal.onCancel = () => resolve(false);
    modal.show();
  });
}

// Smart field dependencies
function setupFieldDependencies() {
  const categoryField = document.getElementById('category');
  const costCodeField = document.getElementById('cost-code');
  
  categoryField.addEventListener('change', function(e) {
    const category = e.target.value;
    
    // Filter cost codes based on category
    filterCostCodes(costCodeField, category);
    
    // Show category-specific help
    showCategoryHelp(category);
  });
}
```

### 5. Responsive Design Patterns
```css
/* Container queries for component-based responsiveness */
.po-form {
  container-type: inline-size;
}

@container (max-width: 600px) {
  .form-row {
    flex-direction: column;
  }
  
  .form-actions {
    position: sticky;
    bottom: 0;
    background: white;
    padding: 1rem;
    border-top: 1px solid var(--gray-300);
  }
}

/* Adaptive layouts based on screen size */
@media (min-width: 768px) {
  .dashboard-grid {
    display: grid;
    grid-template-columns: 1fr 300px;
    gap: 2rem;
  }
  
  .main-content {
    grid-column: 1;
  }
  
  .sidebar {
    grid-column: 2;
  }
}

@media (max-width: 767px) {
  .dashboard-grid {
    display: block;
  }
  
  .sidebar {
    margin-top: 2rem;
  }
}

/* Touch-friendly interactions */
@media (hover: none) and (pointer: coarse) {
  .btn {
    min-height: 52px;
  }
  
  .dropdown-item {
    padding: 12px 16px;
  }
  
  /* Larger tap targets for mobile */
  .close-button {
    width: 48px;
    height: 48px;
  }
}
```

### 6. Performance-Conscious UX
```javascript
// Lazy loading for better perceived performance
function setupLazyLoading() {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        loadContent(entry.target);
        observer.unobserve(entry.target);
      }
    });
  });
  
  document.querySelectorAll('[data-lazy-load]').forEach(el => {
    observer.observe(el);
  });
}

// Optimistic UI updates
function optimisticUpdate(action, rollback) {
  // Apply UI changes immediately
  const uiState = applyOptimisticChanges();
  
  // Perform actual action
  action()
    .then(result => {
      // Confirm changes with server response
      confirmChanges(result);
    })
    .catch(error => {
      // Rollback on failure
      rollback(uiState);
      showError('Action failed. Changes have been reverted.');
    });
}

// Progressive enhancement
function enhanceWithJavaScript() {
  // Check for JavaScript support
  document.documentElement.classList.add('js-enabled');
  
  // Enhance forms with client-side validation
  document.querySelectorAll('form').forEach(form => {
    if (form.noValidate) {
      setupClientSideValidation(form);
    }
  });
  
  // Add keyboard shortcuts
  setupKeyboardShortcuts();
  
  // Enable auto-save for drafts
  setupAutoSave();
}
```

### 7. Micro-Interactions and Feedback
```css
/* Subtle micro-interactions */
.btn {
  transition: all 0.2s ease;
  transform: translateY(0);
}

.btn:hover:not(:disabled) {
  transform: translateY(-1px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.12);
}

.btn:active {
  transform: translateY(0);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12);
}

/* Loading states */
.btn.loading {
  position: relative;
  color: transparent;
}

.btn.loading::after {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 16px;
  height: 16px;
  margin: -8px 0 0 -8px;
  border: 2px solid currentColor;
  border-radius: 50%;
  border-top-color: transparent;
  animation: spin 0.8s linear infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

/* Success animations */
.success-checkmark {
  animation: checkmark 0.6s ease-in-out;
}

@keyframes checkmark {
  0% {
    stroke-dasharray: 0 100;
  }
  100% {
    stroke-dasharray: 100 0;
  }
}
```

### 8. Content Strategy and Microcopy
```html
<!-- Clear, action-oriented labels -->
<button type="submit" class="btn btn-primary">
  Submit P/O for Approval
</button>

<!-- Helpful placeholder text -->
<input type="text" 
       placeholder="e.g., Construction materials for foundation"
       aria-label="Item description">

<!-- Contextual help text -->
<div class="form-help">
  <p>Choose the cost code that best matches this expense. If you're unsure, contact your project manager.</p>
</div>

<!-- Error messages that guide users -->
<div class="error-message" role="alert">
  <strong>Project number is required.</strong>
  You can find this on your project documentation or ask your supervisor.
</div>

<!-- Success messages that confirm actions -->
<div class="success-message" role="alert">
  <strong>P/O submitted successfully!</strong>
  Your supervisor will be notified and you'll receive an email when it's approved.
</div>

<!-- Empty states that guide users -->
<div class="empty-state">
  <h3>No P/Os yet</h3>
  <p>Create your first purchase order to get started.</p>
  <button type="button" class="btn btn-primary">
    Create P/O
  </button>
</div>
```

## Error Handling Patterns

### 1. Field-Level Validation
```javascript
// Validation message patterns
const validationMessages = {
  required: 'This field is required',
  email: 'Please enter a valid email address',
  currency: 'Please enter a valid amount',
  maxLength: (max) => `Maximum ${max} characters allowed`,
  authLimit: (limit) => `Amount exceeds your authorization limit of $${limit}`
};
```

### 2. Form-Level Validation
```javascript
// Form submission validation
function validateForm(formData) {
  const errors = [];
  
  // Required field validation
  if (!formData.projectNumber) {
    errors.push({ field: 'projectNumber', message: 'Project number is required' });
  }
  
  // Business rule validation
  if (formData.totalAmount > userAuthLimit) {
    errors.push({ 
      field: 'totalAmount', 
      message: `Amount exceeds your limit of $${userAuthLimit}`,
      type: 'override_available'
    });
  }
  
  return errors;
}
```

### 3. System Error Messages
```javascript
// User-friendly error messages
const errorMessages = {
  network: 'Connection lost. Please check your internet connection and try again.',
  server: 'Something went wrong on our end. Please try again in a few moments.',
  validation: 'Please correct the highlighted fields and try again.',
  authorization: 'You don\'t have permission to perform this action.',
  timeout: 'Your session has expired. Please log in again.'
};
```

## Performance Guidelines

### 1. Loading States
```html
<!-- Loading spinner for async operations -->
<button class="btn btn-primary" id="submit-btn">
  <span class="btn-text">Submit P/O</span>
  <span class="btn-spinner hidden">
    <i class="spinner" aria-hidden="true"></i>
    Submitting...
  </span>
</button>
```

### 2. Progressive Enhancement
```javascript
// Feature detection and fallbacks
if ('serviceWorker' in navigator) {
  // Enable offline functionality
  navigator.serviceWorker.register('/sw.js');
} else {
  // Fallback for browsers without service worker support
  showOfflineWarning();
}
```

### 3. Image Optimization
```html
<!-- Responsive images with fallbacks -->
<picture>
  <source media="(max-width: 768px)" srcset="receipt-mobile.webp" type="image/webp">
  <source media="(max-width: 768px)" srcset="receipt-mobile.jpg" type="image/jpeg">
  <source srcset="receipt-desktop.webp" type="image/webp">
  <img src="receipt-desktop.jpg" alt="Purchase receipt" loading="lazy">
</picture>
```

## Testing Guidelines

### 1. Component Testing
```javascript
// Test role-based UI behavior
describe('P/O Creation Form', () => {
  test('shows appropriate fields for Foreman role', () => {
    render(<POForm userRole="foreman" />);
    expect(screen.getByText('$1,000 limit')).toBeInTheDocument();
    expect(screen.queryByText('Rolling P/O')).not.toBeInTheDocument();
  });
});
```

### 2. Accessibility Testing
```javascript
// Test keyboard navigation
test('form is keyboard navigable', () => {
  render(<POForm />);
  const firstInput = screen.getByLabelText('Project Number');
  firstInput.focus();
  
  userEvent.tab();
  expect(screen.getByLabelText('Vendor Name')).toHaveFocus();
});
```

### 3. Mobile Testing
```javascript
// Test responsive behavior
test('stacks form elements on mobile', () => {
  Object.defineProperty(window, 'innerWidth', { value: 375 });
  render(<POForm />);
  
  const formRow = screen.getByTestId('form-row');
  expect(formRow).toHaveClass('mobile-stack');
});
```

## Implementation Checklist

### For Developers

#### Code Quality and Standards
- [ ] Use consistent naming conventions throughout codebase (P/O, PM, Super, Cost Code)
- [ ] Follow semantic HTML structure with proper landmarks
- [ ] Implement progressive enhancement (core functionality without JavaScript)
- [ ] Use CSS custom properties for consistent theming
- [ ] Follow BEM or similar CSS methodology for maintainable styles
- [ ] Implement proper error boundaries and fallback UI

#### Accessibility Implementation
- [ ] Add proper ARIA labels, roles, and properties to all interactive elements
- [ ] Ensure logical tab order without using tabindex > 0
- [ ] Implement skip links for keyboard navigation
- [ ] Use semantic HTML elements (main, nav, section, article, aside)
- [ ] Provide alternative text for all images and icons
- [ ] Ensure form labels are properly associated with inputs
- [ ] Implement live regions for dynamic content announcements
- [ ] Add keyboard shortcuts with proper ARIA announcements
- [ ] Ensure focus management for single-page application navigation
- [ ] Test with screen readers (NVDA, JAWS, VoiceOver)

#### Responsive and Mobile Implementation
- [ ] Implement mobile-first responsive design
- [ ] Ensure touch targets are minimum 48px (52px preferred on mobile)
- [ ] Test on actual mobile devices, not just browser dev tools
- [ ] Implement proper viewport meta tag
- [ ] Optimize for one-handed mobile use
- [ ] Test with various screen orientations
- [ ] Implement container queries where appropriate

#### Performance and UX
- [ ] Implement loading states for all async operations
- [ ] Add optimistic UI updates where appropriate
- [ ] Implement lazy loading for non-critical content
- [ ] Ensure critical content loads within 2 seconds
- [ ] Add proper error handling with user-friendly messages
- [ ] Implement auto-save for draft P/Os
- [ ] Add haptic feedback for mobile interactions
- [ ] Respect user preferences (prefers-reduced-motion, prefers-contrast)

#### Role-Based Features
- [ ] Implement role-based UI rendering (Foreman, Super, PM, Admin)
- [ ] Show appropriate authorization limits and warnings
- [ ] Implement supervisor override request workflows
- [ ] Add contextual help based on user role
- [ ] Test all permission levels and edge cases

### For Designers

#### Visual Design Standards
- [ ] Use WCAG AA compliant color palette (4.5:1 contrast minimum)
- [ ] Ensure colors meet contrast requirements in high contrast mode
- [ ] Design consistent component states (default, hover, focus, active, disabled)
- [ ] Create clear visual hierarchy with proper typography scale
- [ ] Design status indicators that don't rely solely on color
- [ ] Use consistent spacing system (8px base unit)
- [ ] Design proper focus indicators (3px outline minimum)

#### Accessibility Design
- [ ] Ensure sufficient color contrast for all text and UI elements
- [ ] Design clear focus indicators that are visible in all contexts
- [ ] Create designs that work without color (use icons, patterns, text)
- [ ] Design touch targets that meet minimum size requirements
- [ ] Consider cognitive load in information architecture
- [ ] Design clear error and success states with multiple indicators
- [ ] Create designs that work with 200% zoom
- [ ] Test designs with high contrast mode enabled

#### Mobile and Responsive Design
- [ ] Design for mobile-first approach
- [ ] Create thumb-friendly navigation patterns
- [ ] Design sticky elements appropriately for mobile
- [ ] Consider landscape and portrait orientations
- [ ] Design for various screen densities
- [ ] Create appropriate breakpoints for content
- [ ] Design offline states and error scenarios

#### User Experience Design
- [ ] Design clear workflow progression indicators
- [ ] Create contextual help and guidance
- [ ] Design empty states that guide users to action
- [ ] Create confirmation dialogs for destructive actions
- [ ] Design loading states that indicate progress
- [ ] Create micro-interactions that provide feedback
- [ ] Design for error prevention, not just error handling
- [ ] Consider cognitive accessibility in information presentation

### For QA and Testing

#### Functional Testing
- [ ] Test all user roles and permission levels (Foreman, Super, PM, Admin)
- [ ] Verify authorization limit enforcement and override workflows
- [ ] Test all P/O workflows (Standard, Rolling, Walk-up)
- [ ] Verify form validation and error handling
- [ ] Test document upload and management features
- [ ] Verify email notifications and P/O delivery
- [ ] Test integration with JobCost and Timberline systems

#### Accessibility Testing
- [ ] Test keyboard-only navigation through all workflows
- [ ] Verify screen reader compatibility (test with NVDA, JAWS, VoiceOver)
- [ ] Test with high contrast mode enabled
- [ ] Verify focus management and visual focus indicators
- [ ] Test with 200% browser zoom
- [ ] Verify ARIA labels and live region announcements
- [ ] Test skip links and landmark navigation
- [ ] Validate color contrast ratios using automated tools
- [ ] Test with voice control software (Dragon NaturallySpeaking)

#### Responsive and Mobile Testing
- [ ] Test on actual mobile devices (iOS and Android)
- [ ] Verify touch target sizes and spacing
- [ ] Test in both portrait and landscape orientations
- [ ] Verify responsive behavior across breakpoints
- [ ] Test with various screen sizes and resolutions
- [ ] Verify mobile-specific interactions (swipe, pinch, etc.)
- [ ] Test offline functionality and network error handling
- [ ] Verify performance on slower devices and networks

#### Cross-Browser and Performance Testing
- [ ] Test in all supported browsers (Chrome, Firefox, Safari, Edge)
- [ ] Verify performance metrics (Core Web Vitals)
- [ ] Test with slow network connections
- [ ] Verify loading states and error handling
- [ ] Test with JavaScript disabled (progressive enhancement)
- [ ] Verify print styles and functionality
- [ ] Test with ad blockers and privacy extensions

#### User Experience Testing
- [ ] Conduct usability testing with actual field personnel
- [ ] Test error message clarity and helpfulness
- [ ] Verify workflow completion rates
- [ ] Test contextual help and guidance effectiveness
- [ ] Verify auto-save and data persistence
- [ ] Test notification timing and relevance
- [ ] Verify search and filtering functionality
- [ ] Test with users who have varying technical expertise

### Accessibility Compliance Checklist

#### WCAG 2.1 AA Requirements
- [ ] **1.1.1 Non-text Content:** All images have appropriate alt text
- [ ] **1.3.1 Info and Relationships:** Semantic markup conveys structure
- [ ] **1.3.2 Meaningful Sequence:** Content order makes sense when linearized
- [ ] **1.4.3 Contrast (Minimum):** 4.5:1 contrast ratio for normal text
- [ ] **1.4.4 Resize Text:** Content is readable at 200% zoom
- [ ] **1.4.10 Reflow:** Content reflows at 320px width
- [ ] **1.4.11 Non-text Contrast:** 3:1 contrast for UI components
- [ ] **2.1.1 Keyboard:** All functionality available via keyboard
- [ ] **2.1.2 No Keyboard Trap:** Focus can move away from components
- [ ] **2.4.1 Bypass Blocks:** Skip links provided for navigation
- [ ] **2.4.3 Focus Order:** Focus order is logical and intuitive
- [ ] **2.4.7 Focus Visible:** Focus indicator is clearly visible
- [ ] **3.1.1 Language of Page:** Page language is identified
- [ ] **3.2.1 On Focus:** Focus doesn't trigger unexpected changes
- [ ] **3.2.2 On Input:** Input doesn't trigger unexpected changes
- [ ] **3.3.1 Error Identification:** Errors are clearly identified
- [ ] **3.3.2 Labels or Instructions:** Form inputs have clear labels
- [ ] **4.1.1 Parsing:** HTML is valid and properly structured
- [ ] **4.1.2 Name, Role, Value:** UI components have accessible names

### Performance Benchmarks
- [ ] **First Contentful Paint:** < 1.5 seconds
- [ ] **Largest Contentful Paint:** < 2.5 seconds
- [ ] **First Input Delay:** < 100 milliseconds
- [ ] **Cumulative Layout Shift:** < 0.1
- [ ] **Time to Interactive:** < 3.5 seconds
- [ ] **Speed Index:** < 3.0 seconds
- [ ] **Total Blocking Time:** < 200 milliseconds

### Security Testing
- [ ] Verify role-based access controls
- [ ] Test input validation and sanitization
- [ ] Verify secure file upload handling
- [ ] Test session management and timeout
- [ ] Verify HTTPS enforcement
- [ ] Test for common vulnerabilities (XSS, CSRF, etc.)
- [ ] Verify audit logging functionality
- [ ] Test data encryption at rest and in transit

This design guide ensures consistent, accessible, and user-friendly interfaces across the IDL Purchase Order Tool while maintaining alignment with business requirements and user needs.

### Form Layout Standards
- Single-column vertical flow for all forms (no zig-zag or two-column layouts)
- Consistent spacing:
  - 32px between sections
  - 24px between individual fields
- Clear visual hierarchy:
  - Form title: 32px margin-bottom
  - Section headers: 24px margin-bottom
  - Field labels: 8px margin-bottom
  - Input fields: 48px height for optimal touch targets
  - Helper text: 8px margin-top
- Mobile-first constraints:
  - Max-width: 100% on mobile, 400px on desktop
  - Padding: 16px (mobile), 24px (desktop)
- Avoid graying out valid fields on error
- Ensure autofill styles don’t override contrast or background design

### Button Design & Action Areas
- Primary actions (e.g., "Submit", "Save", "Continue") must:
  - Use a high-contrast primary color (aligned with brand, max 3-color palette)
  - Span full width on mobile
  - Use clear microcopy: avoid generic terms like “OK” or “Confirm”
- Secondary actions: use outline or subtle background
- Button height: 48px (touch comfort)
- Group related buttons with 16px spacing
- Ensure keyboard accessibility and visible focus state

### Input Patterns and Components
- Field types should match expected data (e.g., `type="email"`, `type="number"`)
- Use dropdowns only if:
  - Options ≤ 7 and easy to scan
- Use searchable selects (autocomplete) for long option lists
- Radio buttons for mutually exclusive short options
- Related inputs should be grouped under section headers

### Validation and Error Handling
- Use inline validation when possible
- Display field-level errors:
  - Specific and actionable messages
  - Red, 12–14px font
  - Positioned directly below the field
- Retain data on failed submit and auto-scroll to first error

### Interaction Feedback
- Show button-level spinners with label change (e.g., "Submitting…")
- Avoid full-page spinners unless absolutely necessary
- Use confirmation dialogs for destructive actions
- On success:
  - Toast confirmation
  - Optional follow-up actions (“View PO”, “Create Another”)

### Responsive Behavior
- Mobile-first layout with adaptive stacking
- Form max width: 400–600px (centered on desktop)
- Collapse complex UI into simpler stacks for smaller viewports
- Avoid fixed headers/footers that block field inputs on mobile

### Typography
- Font sizes:
  - Labels: 14px
  - Inputs: 16px
  - Section headers: 18–20px
  - Titles: 24px
- Font styles: limit to two (e.g., Roboto for base + optional accent)
- Line height: 1.5
- Use system font stack if no custom font

### Accessibility
- Follow WCAG AA contrast ratios
- All inputs must have:
  - Associated visible `<label>` or `aria-label`
  - Clear focus outlines
- Messages should be screen-reader accessible
- Avoid placeholder-only labeling
- Full keyboard operability

### Microcopy Guidelines
- Labels must be short and descriptive
- Placeholder: example formats only
- Helper text: optional hints, format guidance
- Error messages: specific and user-correctable