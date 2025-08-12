# IDL Purchase Order Tool - Frontend Page Specifications

## Overview

This document provides detailed specifications for all frontend pages in the IDL Purchase Order Tool, including layout structures, component arrangements, responsive behavior, and role-based variations. Each page specification includes exact component placement, data requirements, and interaction patterns.

## Page Layout Architecture

### Base Layout Structure
```typescript
interface BaseLayoutProps {
  children: React.ReactNode;
  title: string;
  breadcrumbs?: Breadcrumb[];
  actions?: PageAction[];
  showOfflineIndicator?: boolean;
}

interface PageAction {
  label: string;
  icon?: React.ComponentType;
  onClick: () => void;
  variant?: 'primary' | 'secondary' | 'danger';
  disabled?: boolean;
  requiredPermissions?: string[];
}
```

## Dashboard Page (`/dashboard`)

### Layout Structure
```tsx
<DashboardPage>
  <PageHeader>
    <UserInfo role={userRole} authLimit={authLimit} />
    <OfflineIndicator />
  </PageHeader>
  
  <QuickActions userRole={userRole} permissions={permissions} />
  
  {hasPermission('po:approve') && (
    <PendingApprovalsSection />
  )}
  
  <RecentPOsSection />
  
  {userRole === 'pm' && (
    <StatisticsSection />
  )}
</DashboardPage>
```

### Component Specifications

#### UserInfo Component
```typescript
interface UserInfoProps {
  user: User;
  role: UserRole;
  authLimit: number;
}

// Desktop Layout: Horizontal with role badge
// Mobile Layout: Stacked with collapsible details
```

#### QuickActions Component
```typescript
interface QuickActionsProps {
  userRole: UserRole;
  permissions: string[];
  onCreatePO: (type: POType) => void;
}

// Layout: 3-column grid on desktop, stacked on mobile
// Actions: Create Standard P/O, Create Rolling P/O, Create Walk-up P/O
// Conditional rendering based on permissions
```

#### PendingApprovalsSection
```typescript
interface PendingApprovalsSectionProps {
  approvals: PendingApproval[];
  onApprove: (poId: number) => void;
  onReject: (poId: number) => void;
  onViewDetails: (poId: number) => void;
}

// Desktop: Table with inline actions
// Mobile: Card layout with expandable details
// Max 5 items shown, "View All" link for more
```

#### RecentPOsSection
```typescript
interface RecentPOsSectionProps {
  purchaseOrders: PurchaseOrder[];
  onViewPO: (poId: number) => void;
  onEditPO: (poId: number) => void;
}

// Desktop: Table with 5 columns (P/O Number, Vendor, Amount, Status, Date)
// Mobile: Card layout with key information
// Shows last 10 P/Os, "View All" link
```

### Responsive Breakpoints
- **Desktop (≥1200px)**: 3-column layout for quick actions, full table views
- **Tablet (768px-1199px)**: 2-column layout, condensed tables
- **Mobile (<768px)**: Single column, card-based layouts

## P/O Creation Pages

### Multi-Step Wizard Layout (`/po/create`)
```tsx
<POCreationWizard>
  <WizardHeader>
    <StepIndicator steps={steps} currentStep={currentStep} />
    <DraftIndicator lastSaved={lastSaved} />
  </WizardHeader>
  
  <WizardContent>
    {currentStep === 0 && <ProjectSelectionStep />}
    {currentStep === 1 && <ItemEntryStep />}
    {currentStep === 2 && <ReviewSubmitStep />}
  </WizardContent>
  
  <WizardActions>
    <Button variant="outlined" onClick={onSaveDraft}>Save Draft</Button>
    <Button variant="text" onClick={onDiscard}>Discard</Button>
    <Button onClick={onPrevious} disabled={!canGoPrevious}>Previous</Button>
    <Button variant="contained" onClick={onNext} disabled={!canGoNext}>
      {isLastStep ? 'Submit P/O' : 'Next'}
    </Button>
  </WizardActions>
</POCreationWizard>
```

### Step 1: Project Selection (`ProjectSelectionStep`)
```typescript
interface ProjectSelectionStepProps {
  formData: ProjectSelectionData;
  onFormChange: (data: Partial<ProjectSelectionData>) => void;
  projects: Project[];
  costCodes: CostCode[];
  validation: ValidationErrors;
}

// Layout Components:
// 1. P/O Type Selector (Radio buttons: Standard, Rolling, Walk-up)
// 2. Project Selector (Searchable dropdown with project details)
// 3. Cost Code Selector (Dependent dropdown, loads after project selection)
// 4. Conditional Fields:
//    - Rolling P/O: Ceiling Amount input
//    - Walk-up P/O: Urgency Justification textarea
```

**Desktop Layout:**
```
┌─────────────────────────────────────────────┐
│ P/O Type Selection (Radio Group)            │
├─────────────────────────────────────────────┤
│ Project Selection                           │
│ ┌─────────────────┐ ┌─────────────────────┐ │
│ │ Project Dropdown│ │ Project Details     │ │
│ │                 │ │ Budget: $X,XXX,XXX  │ │
│ └─────────────────┘ │ Status: Active      │ │
│                     └─────────────────────┘ │
├─────────────────────────────────────────────┤
│ Cost Code Selection                         │
│ ┌─────────────────┐ ┌─────────────────────┐ │
│ │ Cost Code       │ │ Code Details        │ │
│ │ Dropdown        │ │ Budget: $XXX,XXX    │ │
│ └─────────────────┘ │ Remaining: $XXX,XXX │ │
│                     └─────────────────────┘ │
├─────────────────────────────────────────────┤
│ Conditional Fields (based on P/O type)     │
└─────────────────────────────────────────────┘
```

### Step 2: Item Entry (`ItemEntryStep`)
```typescript
interface ItemEntryStepProps {
  formData: ItemEntryData;
  onFormChange: (data: Partial<ItemEntryData>) => void;
  onAddLineItem: () => void;
  onRemoveLineItem: (index: number) => void;
  onLineItemChange: (index: number, field: string, value: any) => void;
  validation: ValidationErrors;
}

// Layout Components:
// 1. Vendor Information Section
// 2. Line Items Table (editable)
// 3. Totals Calculation Section
// 4. Notes Section
// 5. File Attachments Section
```

**Desktop Layout:**
```
┌─────────────────────────────────────────────┐
│ Vendor Information                          │
│ ┌─────────────┐ ┌─────────────┐ ┌─────────┐ │
│ │ Vendor Name │ │ Contact     │ │ Email   │ │
│ └─────────────┘ └─────────────┘ └─────────┘ │
├─────────────────────────────────────────────┤
│ Line Items                                  │
│ ┌─────────────────────────────────────────┐ │
│ │ Editable Table with Add/Remove buttons │ │
│ │ Columns: Desc, Qty, Unit, Price, Cat   │ │
│ └─────────────────────────────────────────┘ │
├─────────────────────────────────────────────┤
│ Totals                    │ Notes & Files   │
│ Subtotal: $XXX.XX        │ ┌─────────────┐ │
│ Tax:      $XX.XX         │ │ Notes       │ │
│ Total:    $XXX.XX        │ │             │ │
│                          │ └─────────────┘ │
│                          │ File Upload     │
└─────────────────────────────────────────────┘
```

### Step 3: Review & Submit (`ReviewSubmitStep`)
```typescript
interface ReviewSubmitStepProps {
  formData: POCreationFormData;
  calculatedTotals: POTotals;
  authorizationCheck: AuthorizationResult;
  onSubmit: () => void;
  onEdit: (step: number) => void;
}

// Layout Components:
// 1. P/O Summary Card
// 2. Authorization Status
// 3. Line Items Review (read-only)
// 4. Final Totals
// 5. Submit Actions
```

## P/O List Page (`/po/list`)

### Layout Structure
```tsx
<POListPage>
  <PageHeader title="Purchase Orders">
    <SearchBar onSearch={onSearch} />
    <FilterButton onClick={onShowFilters} />
    <CreatePOButton />
  </PageHeader>
  
  <FiltersPanel expanded={showFilters}>
    <StatusFilter />
    <DateRangeFilter />
    <ProjectFilter />
    <AmountRangeFilter />
  </FiltersPanel>
  
  <POTable>
    <TableHeader sortable columns={columns} />
    <TableBody>
      {purchaseOrders.map(po => (
        <PORow key={po.id} po={po} onRowClick={onViewPO} />
      ))}
    </TableBody>
  </POTable>
  
  <Pagination />
</POListPage>
```

### Table Configuration
```typescript
interface POTableColumn {
  key: string;
  label: string;
  sortable: boolean;
  width?: string;
  render?: (value: any, row: PurchaseOrder) => React.ReactNode;
}

const columns: POTableColumn[] = [
  { key: 'poNumber', label: 'P/O Number', sortable: true, width: '120px' },
  { key: 'status', label: 'Status', sortable: true, width: '100px' },
  { key: 'vendorName', label: 'Vendor', sortable: true, width: '200px' },
  { key: 'projectId', label: 'Project', sortable: true, width: '120px' },
  { key: 'totalAmount', label: 'Amount', sortable: true, width: '100px' },
  { key: 'createdAt', label: 'Created', sortable: true, width: '120px' },
  { key: 'actions', label: 'Actions', sortable: false, width: '100px' }
];
```

### Mobile Transformation
```tsx
// Desktop: Table layout
// Mobile: Card layout with key information
<POCard>
  <CardHeader>
    <PONumberDisplay poNumber={po.poNumber} status={po.status} />
    <StatusBadge status={po.status} />
  </CardHeader>
  <CardContent>
    <Typography variant="body2">Vendor: {po.vendorName}</Typography>
    <Typography variant="body2">Amount: ${po.totalAmount}</Typography>
    <Typography variant="body2">Project: {po.projectId}</Typography>
  </CardContent>
  <CardActions>
    <Button size="small" onClick={() => onView(po.id)}>View</Button>
    {canEdit && <Button size="small" onClick={() => onEdit(po.id)}>Edit</Button>}
  </CardActions>
</POCard>
```

## P/O Detail Page (`/po/:id`)

### Layout Structure
```tsx
<PODetailPage>
  <PageHeader>
    <Breadcrumbs />
    <PONumberDisplay poNumber={po.poNumber} status={po.status} size="large" />
    <ActionMenu actions={availableActions} />
  </PageHeader>
  
  <PODetailTabs>
    <Tab label="Details" value="details">
      <PODetailsSection po={po} />
    </Tab>
    <Tab label="Line Items" value="items">
      <LineItemsSection items={po.lineItems} />
    </Tab>
    <Tab label="Files" value="files">
      <FilesSection files={po.attachments} />
    </Tab>
    <Tab label="History" value="history">
      <AuditHistorySection poId={po.id} />
    </Tab>
  </PODetailTabs>
</PODetailPage>
```

### PODetailsSection
```typescript
interface PODetailsSectionProps {
  po: PurchaseOrder;
  editable?: boolean;
  onEdit?: (field: string, value: any) => void;
}

// Layout: 2-column grid on desktop, stacked on mobile
// Left Column: Project info, vendor info, dates
// Right Column: Totals, status, approval info
```

## Approval Queue Page (`/approvals`)

### Layout Structure
```tsx
<ApprovalQueuePage>
  <PageHeader title="Pending Approvals">
    <ApprovalStats pendingCount={pendingCount} />
    <BulkActionsButton disabled={selectedCount === 0} />
  </PageHeader>
  
  <ApprovalFilters>
    <AmountRangeFilter />
    <ProjectFilter />
    <AgeFilter />
  </ApprovalFilters>
  
  <ApprovalTable>
    <TableHeader>
      <SelectAllCheckbox />
      <SortableColumns />
    </TableHeader>
    <TableBody>
      {approvals.map(approval => (
        <ApprovalRow 
          key={approval.id}
          approval={approval}
          selected={selectedIds.includes(approval.id)}
          onSelect={onSelectApproval}
          onApprove={onApprove}
          onReject={onReject}
        />
      ))}
    </TableBody>
  </ApprovalTable>
  
  <BulkActionPanel visible={selectedCount > 0}>
    <SelectedCount count={selectedCount} />
    <BulkApproveButton />
    <BulkRejectButton />
  </BulkActionPanel>
</ApprovalQueuePage>
```

### ApprovalRow Component
```typescript
interface ApprovalRowProps {
  approval: PendingApproval;
  selected: boolean;
  onSelect: (id: number, selected: boolean) => void;
  onApprove: (id: number) => void;
  onReject: (id: number) => void;
  onViewDetails: (id: number) => void;
}

// Desktop Layout: Table row with inline actions
// Mobile Layout: Expandable card with action buttons
```

## User Management Page (`/users`) - Admin Only

### Layout Structure
```tsx
<UserManagementPage>
  <PageHeader title="User Management">
    <AddUserButton />
    <ImportUsersButton />
  </PageHeader>
  
  <UserFilters>
    <RoleFilter />
    <StatusFilter />
    <SearchFilter />
  </UserFilters>
  
  <UserTable>
    <TableHeader columns={userColumns} />
    <TableBody>
      {users.map(user => (
        <UserRow 
          key={user.id}
          user={user}
          onEdit={onEditUser}
          onToggleStatus={onToggleStatus}
        />
      ))}
    </TableBody>
  </UserTable>
</UserManagementPage>
```

## Error Pages

### 404 Not Found (`/404`)
```tsx
<ErrorPage>
  <ErrorIcon />
  <Typography variant="h4">Page Not Found</Typography>
  <Typography variant="body1">
    The page you're looking for doesn't exist or has been moved.
  </Typography>
  <Button onClick={() => navigate('/dashboard')}>
    Go to Dashboard
  </Button>
</ErrorPage>
```

### 403 Forbidden (`/403`)
```tsx
<ErrorPage>
  <LockIcon />
  <Typography variant="h4">Access Denied</Typography>
  <Typography variant="body1">
    You don't have permission to access this page.
  </Typography>
  <Button onClick={() => navigate(-1)}>
    Go Back
  </Button>
</ErrorPage>
```

## Loading States

### Page Loading
```tsx
<PageSkeleton>
  <SkeletonHeader />
  <SkeletonContent variant="table" rows={10} />
  <SkeletonPagination />
</PageSkeleton>
```

### Component Loading
```tsx
<ComponentSkeleton variant="card" />
<ComponentSkeleton variant="table" rows={5} />
<ComponentSkeleton variant="form" fields={8} />
```

## Empty States

### No P/Os
```tsx
<EmptyState>
  <EmptyIcon />
  <Typography variant="h6">No Purchase Orders</Typography>
  <Typography variant="body2">
    Get started by creating your first P/O
  </Typography>
  <Button variant="contained" onClick={onCreatePO}>
    Create P/O
  </Button>
</EmptyState>
```

### No Search Results
```tsx
<EmptyState>
  <SearchIcon />
  <Typography variant="h6">No Results Found</Typography>
  <Typography variant="body2">
    Try adjusting your search criteria
  </Typography>
  <Button onClick={onClearFilters}>Clear Filters</Button>
</EmptyState>
```

## Responsive Behavior Summary

### Breakpoints
- **xs**: 0px - 599px (Mobile)
- **sm**: 600px - 899px (Mobile landscape/Small tablet)
- **md**: 900px - 1199px (Tablet)
- **lg**: 1200px - 1535px (Desktop)
- **xl**: 1536px+ (Large desktop)

### Layout Transformations
- **Tables → Cards**: On mobile, data tables transform to card layouts
- **Multi-column → Single column**: Forms and content stack vertically
- **Sidebar → Bottom tabs**: Navigation adapts to screen size
- **Inline actions → Menu**: Action buttons collapse to overflow menus

### Touch Optimizations
- **Minimum touch targets**: 44px (48px preferred)
- **Swipe gestures**: Horizontal swipe for tab navigation
- **Pull-to-refresh**: Available on list pages
- **Long press**: Context menus on mobile

---

*This page specification document provides comprehensive layout and component details for all major pages in the IDL Purchase Order Tool. Each page follows consistent patterns while adapting to different screen sizes and user roles.*
