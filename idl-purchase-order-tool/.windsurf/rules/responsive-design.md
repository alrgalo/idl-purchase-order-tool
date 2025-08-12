---
trigger: always_on
---

# IDL Purchase Order Tool - Responsive Design Guide

## Overview

This document defines the responsive design system for the IDL Purchase Order Tool, including breakpoints, layout transformations, component adaptations, and mobile-first implementation patterns. The design prioritizes field staff using mobile devices while providing full functionality on desktop.

## Breakpoint System

### Material-UI Breakpoints
```typescript
const breakpoints = {
  values: {
    xs: 0,      // Mobile portrait
    sm: 600,    // Mobile landscape / Small tablet
    md: 900,    // Tablet portrait
    lg: 1200,   // Desktop / Tablet landscape
    xl: 1536    // Large desktop
  }
};

// Usage in components
const useStyles = makeStyles((theme) => ({
  container: {
    [theme.breakpoints.down('sm')]: {
      padding: theme.spacing(1),
    },
    [theme.breakpoints.up('md')]: {
      padding: theme.spacing(3),
    },
  }
}));
```

### Responsive Utilities
```typescript
// Custom hooks for responsive behavior
export const useResponsive = () => {
  const theme = useTheme();
  const isMobile = useMediaQuery(theme.breakpoints.down('sm'));
  const isTablet = useMediaQuery(theme.breakpoints.between('sm', 'lg'));
  const isDesktop = useMediaQuery(theme.breakpoints.up('lg'));
  
  return { isMobile, isTablet, isDesktop };
};

// Responsive component wrapper
export const ResponsiveContainer: React.FC<ResponsiveContainerProps> = ({
  children,
  mobileLayout,
  desktopLayout
}) => {
  const { isMobile } = useResponsive();
  
  return (
    <Box sx={{
      display: 'flex',
      flexDirection: isMobile ? 'column' : 'row',
      gap: isMobile ? 1 : 3,
      ...isMobile ? mobileLayout : desktopLayout
    }}>
      {children}
    </Box>
  );
};
```

## Layout Transformations

### Navigation Adaptations

#### Desktop Navigation (≥900px)
```tsx
<Drawer
  variant="permanent"
  sx={{
    width: collapsed ? 64 : 240,
    '& .MuiDrawer-paper': {
      width: collapsed ? 64 : 240,
      position: 'fixed',
      height: '100vh',
    },
  }}
>
  <List>
    {navigationItems.map((item) => (
      <ListItem key={item.path}>
        <ListItemIcon><item.icon /></ListItemIcon>
        {!collapsed && <ListItemText primary={item.label} />}
      </ListItem>
    ))}
  </List>
</Drawer>
```

#### Mobile Navigation (<900px)
```tsx
<BottomNavigation
  value={currentPath}
  onChange={handleNavigationChange}
  sx={{
    position: 'fixed',
    bottom: 0,
    left: 0,
    right: 0,
    zIndex: 1000,
    borderTop: 1,
    borderColor: 'divider',
  }}
>
  {mainNavigationItems.slice(0, 4).map((item) => (
    <BottomNavigationAction
      key={item.path}
      label={item.label}
      value={item.path}
      icon={<item.icon />}
    />
  ))}
</BottomNavigation>

{/* Hamburger menu for additional items */}
<IconButton
  onClick={handleMenuOpen}
  sx={{ position: 'fixed', top: 16, right: 16 }}
>
  <MenuIcon />
</IconButton>
```

### Table Transformations

#### Desktop Table (≥900px)
```tsx
<TableContainer component={Paper}>
  <Table>
    <TableHead>
      <TableRow>
        <TableCell>P/O Number</TableCell>
        <TableCell>Status</TableCell>
        <TableCell>Vendor</TableCell>
        <TableCell>Project</TableCell>
        <TableCell align="right">Amount</TableCell>
        <TableCell>Created</TableCell>
        <TableCell>Actions</TableCell>
      </TableRow>
    </TableHead>
    <TableBody>
      {purchaseOrders.map((po) => (
        <TableRow key={po.id}>
          <TableCell>
            <PONumberDisplay poNumber={po.poNumber} status={po.status} />
          </TableCell>
          <TableCell>
            <StatusBadge status={po.status} />
          </TableCell>
          <TableCell>{po.vendorName}</TableCell>
          <TableCell>{po.projectId}</TableCell>
          <TableCell align="right">${po.totalAmount.toFixed(2)}</TableCell>
          <TableCell>{formatDate(po.createdAt)}</TableCell>
          <TableCell>
            <IconButton onClick={() => onView(po.id)}>
              <VisibilityIcon />
            </IconButton>
            <IconButton onClick={() => onEdit(po.id)}>
              <EditIcon />
            </IconButton>
          </TableCell>
        </TableRow>
      ))}
    </TableBody>
  </Table>
</TableContainer>
```

#### Mobile Card Layout (<900px)
```tsx
<Stack spacing={2}>
  {purchaseOrders.map((po) => (
    <Card key={po.id} sx={{ cursor: 'pointer' }} onClick={() => onView(po.id)}>
      <CardHeader
        title={
          <Box display="flex" justifyContent="space-between" alignItems="center">
            <PONumberDisplay poNumber={po.poNumber} status={po.status} size="small" />
            <StatusBadge status={po.status} variant="chip" size="small" />
          </Box>
        }
        subheader={formatDate(po.createdAt)}
      />
      <CardContent sx={{ pt: 0 }}>
        <Typography variant="body2" color="text.secondary" gutterBottom>
          Vendor: {po.vendorName}
        </Typography>
        <Typography variant="body2" color="text.secondary" gutterBottom>
          Project: {po.projectId}
        </Typography>
        <Typography variant="h6" color="primary">
          ${po.totalAmount.toFixed(2)}
        </Typography>
      </CardContent>
      <CardActions>
        <Button size="small" onClick={(e) => { e.stopPropagation(); onView(po.id); }}>
          View
        </Button>
        {canEdit && (
          <Button size="small" onClick={(e) => { e.stopPropagation(); onEdit(po.id); }}>
            Edit
          </Button>
        )}
        <IconButton 
          size="small" 
          onClick={(e) => { e.stopPropagation(); onShowMenu(po.id); }}
        >
          <MoreVertIcon />
        </IconButton>
      </CardActions>
    </Card>
  ))}
</Stack>
```

### Form Layout Adaptations

#### Multi-Column Forms
```tsx
const ResponsiveFormGrid: React.FC<ResponsiveFormGridProps> = ({ children }) => {
  return (
    <Grid container spacing={3}>
      {React.Children.map(children, (child, index) => (
        <Grid 
          item 
          xs={12}           // Full width on mobile
          sm={6}            // Half width on small screens
          md={4}            // Third width on medium screens
          lg={3}            // Quarter width on large screens
          key={index}
        >
          {child}
        </Grid>
      ))}
    </Grid>
  );
};

// Usage in P/O creation form
<ResponsiveFormGrid>
  <TextField label="Vendor Name" fullWidth />
  <TextField label="Contact Person" fullWidth />
  <TextField label="Email" fullWidth />
  <TextField label="Phone" fullWidth />
</ResponsiveFormGrid>
```

#### Step Indicator Adaptations
```tsx
const ResponsiveStepIndicator: React.FC<StepIndicatorProps> = ({ steps, currentStep }) => {
  const { isMobile } = useResponsive();
  
  return (
    <Stepper 
      activeStep={currentStep}
      orientation={isMobile ? 'vertical' : 'horizontal'}
      sx={{
        mb: 3,
        ...(isMobile && {
          '& .MuiStepLabel-label': {
            fontSize: '0.75rem',
          },
        }),
      }}
    >
      {steps.map((step, index) => (
        <Step key={step.label}>
          <StepLabel>
            {isMobile ? step.shortLabel || step.label : step.label}
          </StepLabel>
        </Step>
      ))}
    </Stepper>
  );
};
```

## Component Responsive Patterns

### Dashboard Widgets

#### Desktop Layout (≥1200px)
```tsx
<Grid container spacing={3}>
  <Grid item xs={12} lg={8}>
    <RecentPOsWidget />
  </Grid>
  <Grid item xs={12} lg={4}>
    <Stack spacing={3}>
      <QuickActionsWidget />
      <PendingApprovalsWidget />
      <StatisticsWidget />
    </Stack>
  </Grid>
</Grid>
```

#### Mobile Layout (<600px)
```tsx
<Stack spacing={2}>
  <QuickActionsWidget />
  <PendingApprovalsWidget />
  <RecentPOsWidget />
  <StatisticsWidget />
</Stack>
```

### File Upload Adaptations

#### Desktop File Upload
```tsx
<Box
  sx={{
    border: 2,
    borderColor: 'grey.300',
    borderStyle: 'dashed',
    borderRadius: 1,
    p: 4,
    textAlign: 'center',
    cursor: 'pointer',
    '&:hover': {
      borderColor: 'primary.main',
      backgroundColor: 'action.hover',
    },
  }}
  onClick={handleFileSelect}
>
  <CloudUploadIcon sx={{ fontSize: 48, color: 'grey.400', mb: 2 }} />
  <Typography variant="h6" gutterBottom>
    Drag and drop files here
  </Typography>
  <Typography variant="body2" color="text.secondary">
    or click to select files
  </Typography>
</Box>
```

#### Mobile File Upload with Camera
```tsx
<Stack spacing={2}>
  <Button
    variant="contained"
    startIcon={<CameraAltIcon />}
    onClick={handleCameraCapture}
    fullWidth
    size="large"
    sx={{ py: 2 }}
  >
    Take Photo
  </Button>
  
  <Button
    variant="outlined"
    startIcon={<AttachFileIcon />}
    onClick={handleFileSelect}
    fullWidth
    size="large"
    sx={{ py: 2 }}
  >
    Choose File
  </Button>
  
  {/* File preview for mobile */}
  {selectedFiles.map((file, index) => (
    <Card key={index} sx={{ display: 'flex', alignItems: 'center', p: 1 }}>
      <CardMedia
        component="img"
        sx={{ width: 60, height: 60, borderRadius: 1 }}
        image={file.preview}
        alt={file.name}
      />
      <CardContent sx={{ flex: 1, py: 1 }}>
        <Typography variant="body2" noWrap>
          {file.name}
        </Typography>
        <Typography variant="caption" color="text.secondary">
          {formatFileSize(file.size)}
        </Typography>
      </CardContent>
      <IconButton onClick={() => onRemoveFile(index)}>
        <DeleteIcon />
      </IconButton>
    </Card>
  ))}
</Stack>
```

## Touch Optimizations

### Touch Target Sizing
```typescript
const touchTargetStyles = {
  minHeight: 44,        // iOS minimum
  minWidth: 44,
  padding: '12px 16px', // Comfortable padding
  
  // For small buttons, increase touch area
  '&::before': {
    content: '""',
    position: 'absolute',
    top: -8,
    left: -8,
    right: -8,
    bottom: -8,
    minHeight: 44,
    minWidth: 44,
  },
};

// Apply to interactive elements
const TouchOptimizedButton = styled(Button)(({ theme }) => ({
  ...touchTargetStyles,
  [theme.breakpoints.down('md')]: {
    fontSize: '1rem',     // Larger text on mobile
    padding: '16px 24px', // More padding
  },
}));
```

### Swipe Gestures
```tsx
const useSwipeGestures = (onSwipeLeft?: () => void, onSwipeRight?: () => void) => {
  const [touchStart, setTouchStart] = useState<number | null>(null);
  const [touchEnd, setTouchEnd] = useState<number | null>(null);

  const minSwipeDistance = 50;

  const onTouchStart = (e: React.TouchEvent) => {
    setTouchEnd(null);
    setTouchStart(e.targetTouches[0].clientX);
  };

  const onTouchMove = (e: React.TouchEvent) => {
    setTouchEnd(e.targetTouches[0].clientX);
  };

  const onTouchEnd = () => {
    if (!touchStart || !touchEnd) return;
    
    const distance = touchStart - touchEnd;
    const isLeftSwipe = distance > minSwipeDistance;
    const isRightSwipe = distance < -minSwipeDistance;

    if (isLeftSwipe && onSwipeLeft) onSwipeLeft();
    if (isRightSwipe && onSwipeRight) onSwipeRight();
  };

  return { onTouchStart, onTouchMove, onTouchEnd };
};

// Usage in tab navigation
const SwipeableTabs: React.FC<SwipeableTabsProps> = ({ children, currentTab, onTabChange }) => {
  const swipeHandlers = useSwipeGestures(
    () => onTabChange(Math.min(currentTab + 1, children.length - 1)), // Swipe left = next tab
    () => onTabChange(Math.max(currentTab - 1, 0))                    // Swipe right = previous tab
  );

  return (
    <Box {...swipeHandlers}>
      {children[currentTab]}
    </Box>
  );
};
```

## Typography Scaling

### Responsive Typography
```typescript
const responsiveTypography = {
  h1: {
    fontSize: '2.5rem',
    '@media (max-width:600px)': {
      fontSize: '2rem',
    },
  },
  h2: {
    fontSize: '2rem',
    '@media (max-width:600px)': {
      fontSize: '1.75rem',
    },
  },
  h3: {
    fontSize: '1.75rem',
    '@media (max-width:600px)': {
      fontSize: '1.5rem',
    },
  },
  body1: {
    fontSize: '1rem',
    lineHeight: 1.5,
    '@media (max-width:600px)': {
      fontSize: '0.875rem',
      lineHeight: 1.43,
    },
  },
  // Prevent zoom on iOS when focusing inputs
  fontSize: {
    '@media (max-width:600px)': {
      fontSize: '16px', // Minimum to prevent zoom
    },
  },
};
```

## Performance Optimizations

### Image Responsive Loading
```tsx
const ResponsiveImage: React.FC<ResponsiveImageProps> = ({
  src,
  alt,
  sizes = "100vw",
  priority = false
}) => {
  return (
    <Box
      component="img"
      src={src}
      alt={alt}
      loading={priority ? "eager" : "lazy"}
      sx={{
        width: '100%',
        height: 'auto',
        maxWidth: {
          xs: '100%',
          sm: '600px',
          md: '900px',
          lg: '1200px',
        },
      }}
    />
  );
};
```

### Conditional Component Loading
```tsx
const ConditionalDesktopComponent: React.FC<ConditionalComponentProps> = ({ 
  children, 
  fallback 
}) => {
  const { isDesktop } = useResponsive();
  
  return isDesktop ? <>{children}</> : <>{fallback}</>;
};

// Usage
<ConditionalDesktopComponent
  fallback={<MobileSimplifiedView />}
>
  <ComplexDesktopTable />
</ConditionalDesktopComponent>
```

## Testing Responsive Design

### Responsive Testing Hook
```typescript
const useResponsiveTesting = () => {
  const [currentBreakpoint, setCurrentBreakpoint] = useState<string>('lg');
  
  useEffect(() => {
    const updateBreakpoint = () => {
      const width = window.innerWidth;
      if (width < 600) setCurrentBreakpoint('xs');
      else if (width < 900) setCurrentBreakpoint('sm');
      else if (width < 1200) setCurrentBreakpoint('md');
      else if (width < 1536) setCurrentBreakpoint('lg');
      else setCurrentBreakpoint('xl');
    };

    updateBreakpoint();
    window.addEventListener('resize', updateBreakpoint);
    return () => window.removeEventListener('resize', updateBreakpoint);
  }, []);

  return { currentBreakpoint };
};
```

### Responsive Component Testing
```typescript
describe('Responsive Components', () => {
  test('should render mobile layout on small screens', () => {
    // Mock window.innerWidth
    Object.defineProperty(window, 'innerWidth', {
      writable: true,
      configurable: true,
      value: 500,
    });

    render(<POListPage />);
    
    // Should show card layout instead of table
    expect(screen.queryByRole('table')).not.toBeInTheDocument();
    expect(screen.getAllByTestId('po-card')).toHaveLength(10);
  });

  test('should render desktop layout on large screens', () => {
    Object.defineProperty(window, 'innerWidth', {
      writable: true,
      configurable: true,
      value: 1200,
    });

    render(<POListPage />);
    
    // Should show table layout
    expect(screen.getByRole('table')).toBeInTheDocument();
    expect(screen.queryByTestId('po-card')).not.toBeInTheDocument();
  });
});
```

## Accessibility in Responsive Design

### Focus Management
```tsx
const ResponsiveFocusManagement: React.FC = () => {
  const { isMobile } = useResponsive();
  const focusRef = useRef<HTMLElement>(null);

  useEffect(() => {
    // Adjust focus behavior for mobile
    if (isMobile && focusRef.current) {
      focusRef.current.scrollIntoView({ 
        behavior: 'smooth', 
        block: 'center' 
      });
    }
  }, [isMobile]);

  return (
    <Box ref={focusRef} tabIndex={-1}>
      {/* Content */}
    </Box>
  );
};
```

### Screen Reader Announcements
```tsx
const ResponsiveAnnouncements: React.FC = () => {
  const { isMobile } = useResponsive();
  
  return (
    <Box
      role="status"
      aria-live="polite"
      sx={{ 
        position: 'absolute',
        left: -10000,
        width: 1,
        height: 1,
        overflow: 'hidden',
      }}
    >
      {isMobile ? 'Mobile view active' : 'Desktop view active'}
    </Box>
  );
};
```

---

*This responsive design guide ensures the IDL Purchase Order Tool provides optimal user experience across all device sizes, with particular attention to mobile field workers while maintaining full functionality on desktop systems.*
