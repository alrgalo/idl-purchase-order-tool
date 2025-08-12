# IDL Purchase Order Tool - Component Library

## Overview

This document provides comprehensive specifications for all React components in the IDL Purchase Order Tool. Each component includes TypeScript interfaces, usage examples, and implementation guidelines to ensure consistent UI generation across the application.

## Core Business Components

### PONumberDisplay
Displays P/O numbers with consistent formatting and status-based styling.

```typescript
interface PONumberDisplayProps {
  poNumber: string;
  status: POStatus;
  clickable?: boolean;
  size?: 'small' | 'medium' | 'large';
  showCopyButton?: boolean;
  onClick?: (poNumber: string) => void;
}

type POStatus = 'draft' | 'submitted' | 'approved' | 'rejected' | 'cancelled' | 'closed';
```

**Usage Example:**
```tsx
<PONumberDisplay 
  poNumber="25301-PO-023"
  status="approved"
  clickable={true}
  size="medium"
  showCopyButton={true}
  onClick={(poNumber) => navigate(`/po/${poNumber}`)}
/>
```

### StatusBadge
Displays P/O status with consistent visual treatment.

```typescript
interface StatusBadgeProps {
  status: POStatus;
  variant?: 'pill' | 'chip' | 'inline';
  showIcon?: boolean;
  size?: 'small' | 'medium';
}
```

**Usage:**
```tsx
<StatusBadge status="approved" variant="pill" showIcon={true} />
```

### AuthorizationLimitIndicator
Shows user authorization limits with visual warnings for over-limit amounts.

```typescript
interface AuthorizationLimitIndicatorProps {
  userRole: UserRole;
  authorizationLimit: number;
  currentAmount?: number;
  showWarning?: boolean;
  variant?: 'badge' | 'inline' | 'detailed';
}

type UserRole = 'foreman' | 'super' | 'pm' | 'admin';
```

## Form Components

### CurrencyInput
Specialized input for currency values with proper formatting.

```typescript
interface CurrencyInputProps {
  value: number | '';
  onChange: (value: number | '') => void;
  label?: string;
  error?: boolean;
  helperText?: string;
  required?: boolean;
  disabled?: boolean;
  min?: number;
  max?: number;
}
```

### ProjectCostCodeSelector
Combined selector for project and cost code with dependent loading.

```typescript
interface ProjectCostCodeSelectorProps {
  projectId: string;
  costCode: string;
  onProjectChange: (projectId: string) => void;
  onCostCodeChange: (costCode: string) => void;
  error?: {
    project?: string;
    costCode?: string;
  };
  required?: boolean;
  disabled?: boolean;
}
```

### MultiStepFormWrapper
Container for multi-step P/O creation forms.

```typescript
interface MultiStepFormWrapperProps {
  steps: StepConfig[];
  currentStep: number;
  onStepChange: (step: number) => void;
  onSaveDraft: () => void;
  onSubmit: () => void;
  onDiscard: () => void;
  canGoNext: boolean;
  canGoPrevious: boolean;
  children: React.ReactNode;
}

interface StepConfig {
  label: string;
  description?: string;
  optional?: boolean;
  validation?: (data: any) => boolean;
}
```

## Layout Components

### StepIndicator
Visual progress indicator for multi-step forms.

```typescript
interface StepIndicatorProps {
  steps: StepConfig[];
  currentStep: number;
  completedSteps: number[];
  onStepClick?: (stepIndex: number) => void;
  variant?: 'horizontal' | 'vertical';
}
```

### ResponsiveNavigation
Adaptive navigation that switches between sidebar and bottom tabs.

```typescript
interface ResponsiveNavigationProps {
  currentPath: string;
  userRole: UserRole;
  userPermissions: string[];
  onNavigate: (path: string) => void;
  collapsed?: boolean;
  onToggleCollapse?: () => void;
}
```

**Navigation Items Configuration:**
```typescript
const navigationItems: NavItem[] = [
  { path: '/dashboard', label: 'Dashboard', icon: DashboardIcon },
  { path: '/po/create', label: 'Create P/O', icon: AddIcon, requiredPermissions: ['po:create'] },
  { path: '/po/list', label: 'My P/Os', icon: ListIcon },
  { path: '/approvals', label: 'Approvals', icon: ApprovalIcon, requiredPermissions: ['po:approve'] },
  { path: '/reports', label: 'Reports', icon: BarChartIcon, roles: ['pm', 'admin'] }
];
```

## Data Display Components

### POLineItemsTable
Editable table for P/O line items with inline editing.

```typescript
interface POLineItemsTableProps {
  lineItems: POLineItem[];
  onLineItemChange: (index: number, field: keyof POLineItem, value: any) => void;
  onAddLineItem: () => void;
  onRemoveLineItem: (index: number) => void;
  editable?: boolean;
  showTotals?: boolean;
  errors?: Record<number, Record<string, string>>;
}

interface POLineItem {
  id?: number;
  lineNumber: number;
  description: string;
  quantity: number;
  unit: string;
  unitPrice: number;
  category: 'LA' | 'EQ' | 'MA' | 'SC';
  taxCode: 'GST2' | 'PST7' | 'GP' | 'GST_0' | 'XMPT';
  lineTotal: number;
}
```

### POListTable
Displays list of P/Os with filtering and sorting.

```typescript
interface POListTableProps {
  purchaseOrders: PurchaseOrder[];
  onRowClick: (po: PurchaseOrder) => void;
  onStatusFilter: (status: POStatus[]) => void;
  onSort: (field: string, direction: 'asc' | 'desc') => void;
  loading?: boolean;
  userRole: UserRole;
  showActions?: boolean;
}
```

### ApprovalQueueTable
Specialized table for approval workflows with bulk actions.

```typescript
interface ApprovalQueueTableProps {
  pendingApprovals: PendingApproval[];
  onApprove: (poIds: number[], comments?: string) => void;
  onReject: (poIds: number[], reason: string) => void;
  onBulkAction: (action: 'approve' | 'reject', poIds: number[]) => void;
  loading?: boolean;
}
```

## Utility Components

### OfflineIndicator
Shows connection status and sync state.

```typescript
interface OfflineIndicatorProps {
  isOnline: boolean;
  pendingSyncCount?: number;
  lastSyncTime?: Date;
}
```

### FileUploadZone
Drag-and-drop file upload with camera integration.

```typescript
interface FileUploadZoneProps {
  onFilesSelected: (files: File[]) => void;
  acceptedTypes?: string[];
  maxFileSize?: number;
  maxFiles?: number;
  showCamera?: boolean;
  disabled?: boolean;
}
```

### LoadingState
Consistent loading indicators and skeleton screens.

```typescript
interface LoadingStateProps {
  variant: 'spinner' | 'skeleton' | 'progress';
  size?: 'small' | 'medium' | 'large';
  text?: string;
  progress?: number; // for progress variant
}
```

## Theme Configuration

### Color Palette
```typescript
export const theme = {
  palette: {
    primary: {
      main: '#1565c0',
      light: '#1976d2',
      dark: '#0d47a1'
    },
    roles: {
      foreman: '#2196f3',
      super: '#ff9800', 
      pm: '#4caf50',
      admin: '#9c27b0'
    },
    status: {
      draft: '#757575',
      submitted: '#ffc107',
      approved: '#4caf50',
      rejected: '#f44336',
      cancelled: '#9e9e9e',
      closed: '#607d8b'
    }
  }
};
```

### Typography Scale
```typescript
typography: {
  h1: { fontSize: '2.5rem', fontWeight: 600 },
  h2: { fontSize: '2rem', fontWeight: 600 },
  h3: { fontSize: '1.75rem', fontWeight: 600 },
  h4: { fontSize: '1.5rem', fontWeight: 600 },
  h5: { fontSize: '1.25rem', fontWeight: 600 },
  h6: { fontSize: '1.125rem', fontWeight: 600 },
  body1: { fontSize: '1rem', lineHeight: 1.5 },
  body2: { fontSize: '0.875rem', lineHeight: 1.43 },
  caption: { fontSize: '0.75rem', lineHeight: 1.33 }
}
```

### Spacing System
```typescript
spacing: {
  xs: '4px',
  sm: '8px',
  md: '16px',
  lg: '24px',
  xl: '32px',
  xxl: '48px'
}
```

## Component Usage Guidelines

### Accessibility Requirements
- All interactive components must have proper ARIA labels
- Form inputs require associated labels or aria-labelledby
- Focus management for modal dialogs and multi-step forms
- Color contrast ratios must meet WCAG 2.1 AA standards
- Keyboard navigation support for all interactive elements

### Mobile Considerations
- Touch targets minimum 44px (48px preferred)
- Responsive breakpoints: xs: 0px, sm: 600px, md: 900px, lg: 1200px, xl: 1536px
- Bottom navigation for mobile, sidebar for desktop
- Camera integration for file uploads on mobile devices
- Offline support with local storage and sync indicators

### Performance Guidelines
- Use React.memo for expensive components
- Implement virtualization for large lists (>100 items)
- Lazy load images and file attachments
- Code splitting for route-based components
- Debounce search inputs and API calls

### Error Handling
- Consistent error message formatting
- Form validation with real-time feedback
- Network error recovery with retry mechanisms
- Graceful degradation for offline scenarios
- User-friendly error messages with actionable guidance

---

*This component library ensures consistent, accessible, and performant UI components across the IDL Purchase Order Tool. All components should be implemented following these specifications for optimal code generation and user experience.*
