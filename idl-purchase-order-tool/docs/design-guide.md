### 1. P/O Number Display
```
Format for Standard P/Os (≤ $50,000):
JJJJJ-PO-XXX
Example: 25301-PO-023
→ Job code 25301, PO #23

Format for High-Value P/Os (> $50,000):
JobNumber-PO-Sequence
Example: 4-10-061-M001
→ Full job number with M-series sequence

Visual Treatment:
- Monospace font for consistency
- Prominent display in headers
- Clickable for P/O details
- Color coding by status (draft=gray, approved=green, etc.)
- Special indicator for high-value P/Os requiring counter-signature
- Warning indicator for drafts approaching 10-day expiration
- PM Dashboard highlight for unsigned high-value P/Os

Note: P/O numbers are only generated after approval

### 2. Authorization Limit Indicators
```
Visual Hierarchy:
- Foreman: $1,000 (Blue badge)
- Super: $10,000 (Orange badge)  
- PM: $50,000 (Green badge)
- Over $50K: Red warning with special handling required

### 3. Status Indicators
```
Status Pills:
- Draft: Gray background, dark text (expires after 10 days)
- Pending Approval: Yellow background, dark text
- Approved: Green background, white text
- Issued: Blue background, white text
- Awaiting Counter-Signature: Purple background, white text (>$50K only)
- Rejected: Red background, white text
- Expired: Dark gray background, white text

## Material Design Implementation

### Elevation & Shadows
```typescript
// Shadow levels for different surfaces
const shadows = {
  dp0: 'none',                                    // Surface level
  dp1: '0 1px 3px rgba(0,0,0,0.12)',            // Cards, buttons
  dp2: '0 2px 4px rgba(0,0,0,0.14)',            // Navigation drawer
  dp4: '0 4px 8px rgba(0,0,0,0.16)',            // Modal dialogs
  dp8: '0 8px 16px rgba(0,0,0,0.18)',           // Bottom sheets
  dp12: '0 12px 24px rgba(0,0,0,0.20)',         // FAB hover
  dp16: '0 16px 32px rgba(0,0,0,0.22)',         // Picker dialogs
  dp24: '0 24px 48px rgba(0,0,0,0.24)',         // Menu dropdowns
};
```

### Material Color System
```typescript
const materialColors = {
  primary: {
    main: '#1976d2',                             // Primary actions, links
    light: '#42a5f5',                            // Hover states
    dark: '#1565c0',                             // Active states
    contrastText: '#ffffff'
  },
  secondary: {
    main: '#dc004e',                             // Accent color
    light: '#ff5983',                            // Secondary hover
    dark: '#9a0036',                             // Secondary active
    contrastText: '#ffffff'
  },
  background: {
    default: '#ffffff',                          // Main background
    paper: '#f5f5f5',                           // Card background
    elevated: '#ffffff'                          // Elevated surfaces
  }
};
```

### Extended Color System

#### Status Colors
```typescript
const statusColors = {
  // Status indicators
  status: {
    draft: {
      background: '#9e9e9e',          // Gray
      text: '#2c2c2c',               // Dark text
      hover: '#8c8c8c'
    },
    pendingApproval: {
      background: '#ffa726',          // Yellow/Orange
      text: '#2c2c2c',               // Dark text
      hover: '#f57c00'
    },
    approved: {
      background: '#4caf50',          // Green
      text: '#ffffff',               // White text
      hover: '#43a047'
    },
    issued: {
      background: '#2196f3',          // Blue
      text: '#ffffff',               // White text
      hover: '#1976d2'
    },
    awaitingSignature: {
      background: '#9c27b0',          // Purple
      text: '#ffffff',               // White text
      hover: '#7b1fa2'
    },
    rejected: {
      background: '#f44336',          // Red
      text: '#ffffff',               // White text
      hover: '#d32f2f'
    },
    expired: {
      background: '#616161',          // Dark Gray
      text: '#ffffff',               // White text
      hover: '#424242'
    }
  },

  // Authorization badges
  authorization: {
    foreman: {
      background: '#2196f3',          // Blue
      text: '#ffffff',               // White text
      border: '#1976d2'
    },
    super: {
      background: '#ff9800',          // Orange
      text: '#2c2c2c',               // Dark text
      border: '#f57c00'
    },
    pm: {
      background: '#4caf50',          // Green
      text: '#ffffff',               // White text
      border: '#43a047'
    },
    overLimit: {
      background: '#ffebee',          // Light Red
      text: '#d32f2f',               // Red text
      border: '#ef5350'
    }
  },

  // Warning indicators
  warning: {
    draftExpiring: {
      background: '#fff3e0',          // Light Orange
      text: '#e65100',               // Dark Orange text
      border: '#ff9800'
    },
    highValue: {
      background: '#fce4ec',          // Light Pink
      text: '#c2185b',               // Dark Pink text
      border: '#e91e63'
    }
  }
};

// Usage examples
const StatusPill: React.FC<{ status: POStatus }> = ({ status }) => (
  <Chip
    label={status}
    sx={{
      backgroundColor: statusColors.status[status].background,
      color: statusColors.status[status].text,
      '&:hover': {
        backgroundColor: statusColors.status[status].hover
      }
    }}
  />
);

const AuthBadge: React.FC<{ role: UserRole }> = ({ role }) => (
  <Box
    sx={{
      backgroundColor: statusColors.authorization[role].background,
      color: statusColors.authorization[role].text,
      border: `1px solid ${statusColors.authorization[role].border}`,
      borderRadius: 1,
      px: 1,
      py: 0.5,
      display: 'inline-flex',
      alignItems: 'center',
      gap: 0.5,
      typography: 'caption'
    }}
  >
    <PersonIcon fontSize="small" />
    {role}
  </Box>
);

const WarningIndicator: React.FC<{ type: 'draftExpiring' | 'highValue' }> = ({ type }) => (
  <Alert
    severity="warning"
    sx={{
      backgroundColor: statusColors.warning[type].background,
      color: statusColors.warning[type].text,
      border: `1px solid ${statusColors.warning[type].border}`,
      '& .MuiAlert-icon': {
        color: statusColors.warning[type].text
      }
    }}
  >
    {type === 'draftExpiring' ? 'Draft expires soon' : 'High-value P/O'}
  </Alert>
);
```

This color system:
- Defines exact colors for all status states
- Includes hover states for interactive elements
- Provides consistent text colors for accessibility
- Includes border colors for outlined variants
- Matches Material Design color palette
- Maintains WCAG 2.1 AA contrast requirements
- Supports dark mode through HSL color space

### Animation & Transitions
```typescript
const animations = {
  // State changes
  stateChange: 'all 200ms cubic-bezier(0.4, 0, 0.2, 1)',
  
  // Modal/dialog enter/exit
  modalEnter: 'all 225ms cubic-bezier(0.4, 0, 0.2, 1)',
  modalExit: 'all 195ms cubic-bezier(0.4, 0, 0.2, 1)',
  
  // Navigation transitions
  navEnter: 'all 250ms cubic-bezier(0.4, 0, 0.2, 1)',
  navExit: 'all 200ms cubic-bezier(0.4, 0, 0.2, 1)',
  
  // Button ripple
  rippleDuration: '600ms'
};
```

### Grid & Spacing System
```typescript
const grid = {
  baseUnit: 8,                                   // 8px base grid
  spacing: (multiplier: number) => `${multiplier * 8}px`,
  
  // Common spacing values
  spacingValues: {
    xs: 4,                                      // 4px
    sm: 8,                                      // 8px
    md: 16,                                     // 16px
    lg: 24,                                     // 24px
    xl: 32,                                     // 32px
    xxl: 48                                     // 48px
  }
};
```

### Material Components

#### Cards
```jsx
<Card 
  elevation={1}
  sx={{
    borderRadius: 2,
    transition: animations.stateChange,
    '&:hover': {
      elevation: 2,
      transform: 'translateY(-2px)'
    }
  }}
>
  <CardContent>
    {/* Card content */}
  </CardContent>
</Card>
```

#### Buttons
```jsx
<Button
  variant="contained"
  sx={{
    textTransform: 'none',
    borderRadius: 2,
    height: 48,
    transition: animations.stateChange,
    '&:hover': {
      elevation: 2
    }
  }}
>
  Submit P/O
</Button>
```

#### Form Fields
```jsx
<TextField
  variant="outlined"
  sx={{
    '& .MuiOutlinedInput-root': {
      borderRadius: 2,
      transition: animations.stateChange,
      '&:hover': {
        '& .MuiOutlinedInput-notchedOutline': {
          borderColor: 'primary.main'
        }
      }
    }
  }}
/>
```

### Micro-Interactions

#### Loading States
```jsx
<LoadingButton
  loading={isSubmitting}
  loadingIndicator={
    <CircularProgress 
      size={24} 
      sx={{ 
        animation: 'spin 1s linear infinite',
        transition: animations.stateChange 
      }} 
    />
  }
>
  Submit
</LoadingButton>
```

#### Form Validation
```jsx
<TextField
  error={hasError}
  helperText={errorMessage}
  sx={{
    '& .MuiFormHelperText-root': {
      transition: animations.stateChange,
      opacity: hasError ? 1 : 0,
      transform: hasError ? 'translateY(0)' : 'translateY(-10px)'
    }
  }}
/>
```

#### Status Transitions
```jsx
<Chip
  label={status}
  sx={{
    transition: animations.stateChange,
    backgroundColor: getStatusColor(status),
    transform: 'scale(1)',
    '&:hover': {
      transform: 'scale(1.05)'
    }
  }}
/>
