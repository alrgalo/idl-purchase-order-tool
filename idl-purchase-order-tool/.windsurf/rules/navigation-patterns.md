---
trigger: always_on
---

# Navigation Patterns Guide

## Overview

This document outlines navigation patterns for the IDL Purchase Order Tool, including responsive sidebar/bottom tab navigation, role-based menu systems, and routing strategies.

## Responsive Navigation Architecture

### Desktop Navigation (Sidebar)

**Primary Sidebar Implementation:**
```typescript
const DesktopSidebar = () => {
  const { user } = useAuth();
  const location = useLocation();
  const [collapsed, setCollapsed] = useState(false);

  const navigationItems = useMemo(() => [
    {
      label: 'Purchase Orders',
      path: '/purchase-orders',
      icon: <ReceiptIcon />,
      roles: ['foreman', 'super', 'pm', 'admin'],
      children: [
        { label: 'All P/Os', path: '/purchase-orders', icon: <ListIcon /> },
        { label: 'Create P/O', path: '/purchase-orders/create', icon: <AddIcon /> },
        { label: 'Drafts', path: '/purchase-orders/drafts', icon: <DraftsIcon /> }
      ]
    },
    {
      label: 'Approvals',
      path: '/approvals',
      icon: <ApprovalIcon />,
      roles: ['super', 'pm', 'admin'],
      badge: useApprovalCount()
    },
    {
      label: 'Reports',
      path: '/reports',
      icon: <BarChartIcon />,
      roles: ['pm', 'admin']
    },
    {
      label: 'Administration',
      path: '/admin',
      icon: <SettingsIcon />,
      roles: ['admin'],
      children: [
        { label: 'Users', path: '/admin/users', icon: <PeopleIcon /> },
        { label: 'Vendors', path: '/admin/vendors', icon: <BusinessIcon /> },
        { label: 'Projects', path: '/admin/projects', icon: <FolderIcon /> }
      ]
    }
  ], []);

  const visibleItems = navigationItems.filter(item => 
    hasAnyRole(user?.role, item.roles)
  );

  return (
    <Drawer
      variant="permanent"
      sx={{
        width: collapsed ? 64 : 280,
        flexShrink: 0,
        display: { xs: 'none', md: 'block' },
        '& .MuiDrawer-paper': {
          width: collapsed ? 64 : 280,
          boxSizing: 'border-box',
          transition: 'width 0.3s ease'
        }
      }}
    >
      <Box sx={{ display: 'flex', flexDirection: 'column', height: '100%' }}>
        {/* Header */}
        <Box sx={{ p: 2, display: 'flex', alignItems: 'center', justifyContent: 'space-between' }}>
          {!collapsed && (
            <Typography variant="h6" sx={{ fontWeight: 'bold' }}>
              IDL P/O Tool
            </Typography>
          )}
          <IconButton onClick={() => setCollapsed(!collapsed)} size="small">
            {collapsed ? <ChevronRightIcon /> : <ChevronLeftIcon />}
          </IconButton>
        </Box>

        <Divider />

        {/* Navigation Items */}
        <List sx={{ flexGrow: 1, pt: 1 }}>
          {visibleItems.map(item => (
            <NavigationItem
              key={item.path}
              item={item}
              collapsed={collapsed}
              currentPath={location.pathname}
            />
          ))}
        </List>

        {/* User Profile Section */}
        <Box sx={{ p: 2, borderTop: 1, borderColor: 'divider' }}>
          <UserProfileSection collapsed={collapsed} />
        </Box>
      </Box>
    </Drawer>
  );
};
```

**Collapsible Navigation Item:**
```typescript
const NavigationItem = ({ item, collapsed, currentPath }) => {
  const [expanded, setExpanded] = useState(false);
  const isActive = currentPath.startsWith(item.path);
  const hasChildren = item.children && item.children.length > 0;

  const handleClick = () => {
    if (hasChildren && !collapsed) {
      setExpanded(!expanded);
    } else {
      navigate(item.path);
    }
  };

  return (
    <>
      <ListItemButton
        onClick={handleClick}
        selected={isActive}
        sx={{
          minHeight: 48,
          px: 2.5,
          justifyContent: collapsed ? 'center' : 'initial'
        }}
      >
        <ListItemIcon
          sx={{
            minWidth: 0,
            mr: collapsed ? 0 : 3,
            justifyContent: 'center'
          }}
        >
          <Badge badgeContent={item.badge} color="error">
            {item.icon}
          </Badge>
        </ListItemIcon>
        
        {!collapsed && (
          <>
            <ListItemText primary={item.label} />
            {hasChildren && (
              <ExpandMoreIcon
                sx={{
                  transform: expanded ? 'rotate(180deg)' : 'rotate(0deg)',
                  transition: 'transform 0.3s'
                }}
              />
            )}
          </>
        )}
      </ListItemButton>

      {/* Submenu Items */}
      {hasChildren && !collapsed && (
        <Collapse in={expanded} timeout="auto" unmountOnExit>
          <List component="div" disablePadding>
            {item.children.map(child => (
              <ListItemButton
                key={child.path}
                component={Link}
                to={child.path}
                selected={currentPath === child.path}
                sx={{ pl: 4 }}
              >
                <ListItemIcon sx={{ minWidth: 40 }}>
                  {child.icon}
                </ListItemIcon>
                <ListItemText primary={child.label} />
              </ListItemButton>
            ))}
          </List>
        </Collapse>
      )}
    </>
  );
};
```

### Mobile Navigation (Bottom Tabs)

**Bottom Tab Navigation:**
```typescript
const MobileBottomNavigation = () => {
  const location = useLocation();
  const navigate = useNavigate();
  const { user } = useAuth();
  const approvalCount = useApprovalCount();

  const tabs = useMemo(() => [
    {
      label: 'P/Os',
      value: '/purchase-orders',
      icon: <ReceiptIcon />,
      roles: ['foreman', 'super', 'pm', 'admin']
    },
    {
      label: 'Create',
      value: '/purchase-orders/create',
      icon: <AddCircleIcon />,
      roles: ['foreman', 'super', 'pm', 'admin']
    },
    {
      label: 'Approvals',
      value: '/approvals',
      icon: (
        <Badge badgeContent={approvalCount} color="error">
          <ApprovalIcon />
        </Badge>
      ),
      roles: ['super', 'pm', 'admin']
    },
    {
      label: 'More',
      value: '/more',
      icon: <MoreHorizIcon />,
      roles: ['foreman', 'super', 'pm', 'admin']
    }
  ], [approvalCount]);

  const visibleTabs = tabs.filter(tab => 
    hasAnyRole(user?.role, tab.roles)
  );

  const getCurrentTab = () => {
    const currentPath = location.pathname;
    return visibleTabs.find(tab => 
      currentPath.startsWith(tab.value)
    )?.value || visibleTabs[0]?.value;
  };

  return (
    <Paper
      sx={{
        position: 'fixed',
        bottom: 0,
        left: 0,
        right: 0,
        zIndex: 1000,
        display: { xs: 'block', md: 'none' }
      }}
      elevation={8}
    >
      <BottomNavigation
        value={getCurrentTab()}
        onChange={(_, newValue) => {
          if (newValue === '/more') {
            // Handle more menu
            setMoreMenuOpen(true);
          } else {
            navigate(newValue);
          }
        }}
        sx={{ height: 64 }}
      >
        {visibleTabs.map(tab => (
          <BottomNavigationAction
            key={tab.value}
            label={tab.label}
            value={tab.value}
            icon={tab.icon}
            sx={{
              minWidth: 'auto',
              '&.Mui-selected': {
                color: 'primary.main'
              }
            }}
          />
        ))}
      </BottomNavigation>
    </Paper>
  );
};
```

**Mobile "More" Menu:**
```typescript
const MobileMoreMenu = ({ open, onClose }) => {
  const { user } = useAuth();
  const navigate = useNavigate();

  const moreItems = [
    {
      label: 'Reports',
      path: '/reports',
      icon: <BarChartIcon />,
      roles: ['pm', 'admin']
    },
    {
      label: 'Profile',
      path: '/profile',
      icon: <PersonIcon />,
      roles: ['foreman', 'super', 'pm', 'admin']
    },
    {
      label: 'Settings',
      path: '/settings',
      icon: <SettingsIcon />,
      roles: ['foreman', 'super', 'pm', 'admin']
    },
    {
      label: 'Help',
      path: '/help',
      icon: <HelpIcon />,
      roles: ['foreman', 'super', 'pm', 'admin']
    }
  ];

  const visibleItems = moreItems.filter(item => 
    hasAnyRole(user?.role, item.roles)
  );

  return (
    <SwipeableDrawer
      anchor="bottom"
      open={open}
      onClose={onClose}
      onOpen={() => {}}
      sx={{ display: { xs: 'block', md: 'none' } }}
    >
      <Box sx={{ p: 2 }}>
        <Typography variant="h6" sx={{ mb: 2 }}>
          More Options
        </Typography>
        <List>
          {visibleItems.map(item => (
            <ListItem key={item.path} disablePadding>
              <ListItemButton
                onClick={() => {
                  navigate(item.path);
                  onClose();
                }}
              >
                <ListItemIcon>{item.icon}</ListItemIcon>
                <ListItemText primary={item.label} />
              </ListItemButton>
            </ListItem>
          ))}
        </List>
      </Box>
    </SwipeableDrawer>
  );
};
```

## Role-Based Menu System

### Permission-Based Navigation

**Role Permission Utilities:**
```typescript
// Role hierarchy and permissions
const ROLE_HIERARCHY = {
  admin: ['admin', 'pm', 'super', 'foreman'],
  pm: ['pm', 'super', 'foreman'],
  super: ['super', 'foreman'],
  foreman: ['foreman']
};

const NAVIGATION_PERMISSIONS = {
  'purchase-orders': ['foreman', 'super', 'pm', 'admin'],
  'purchase-orders.create': ['foreman', 'super', 'pm', 'admin'],
  'purchase-orders.approve': ['super', 'pm', 'admin'],
  'purchase-orders.delete': ['pm', 'admin'],
  'approvals': ['super', 'pm', 'admin'],
  'reports': ['pm', 'admin'],
  'admin': ['admin'],
  'admin.users': ['admin'],
  'admin.vendors': ['admin'],
  'admin.projects': ['admin']
};

export const hasPermission = (userRole: string, permission: string): boolean => {
  const allowedRoles = NAVIGATION_PERMISSIONS[permission];
  return allowedRoles ? allowedRoles.includes(userRole) : false;
};

export const hasAnyRole = (userRole: string, requiredRoles: string[]): boolean => {
  return requiredRoles.includes(userRole);
};

export const canAccessRoute = (userRole: string, route: string): boolean => {
  // Check if user can access specific route
  const routePermissions = Object.keys(NAVIGATION_PERMISSIONS).filter(perm => 
    route.startsWith(`/${perm.replace('.', '/')}`)
  );
  
  return routePermissions.some(perm => hasPermission(userRole, perm));
};
```

**Dynamic Menu Generation:**
```typescript
const useNavigationMenu = () => {
  const { user } = useAuth();
  
  return useMemo(() => {
    const baseMenu = [
      {
        id: 'purchase-orders',
        label: 'Purchase Orders',
        icon: <ReceiptIcon />,
        path: '/purchase-orders',
        permission: 'purchase-orders',
        children: [
          {
            id: 'po-list',
            label: 'All P/Os',
            path: '/purchase-orders',
            permission: 'purchase-orders'
          },
          {
            id: 'po-create',
            label: 'Create P/O',
            path: '/purchase-orders/create',
            permission: 'purchase-orders.create'
          },
          {
            id: 'po-drafts',
            label: 'My Drafts',
            path: '/purchase-orders/drafts',
            permission: 'purchase-orders'
          }
        ]
      },
      {
        id: 'approvals',
        label: 'Approvals',
        icon: <ApprovalIcon />,
        path: '/approvals',
        permission: 'approvals',
        badge: 'approval-count'
      },
      {
        id: 'reports',
        label: 'Reports',
        icon: <BarChartIcon />,
        path: '/reports',
        permission: 'reports',
        children: [
          {
            id: 'po-reports',
            label: 'P/O Reports',
            path: '/reports/purchase-orders',
            permission: 'reports'
          },
          {
            id: 'budget-reports',
            label: 'Budget Reports',
            path: '/reports/budget',
            permission: 'reports'
          }
        ]
      },
      {
        id: 'admin',
        label: 'Administration',
        icon: <SettingsIcon />,
        path: '/admin',
        permission: 'admin',
        children: [
          {
            id: 'admin-users',
            label: 'Users',
            path: '/admin/users',
            permission: 'admin.users'
          },
          {
            id: 'admin-vendors',
            label: 'Vendors',
            path: '/admin/vendors',
            permission: 'admin.vendors'
          },
          {
            id: 'admin-projects',
            label: 'Projects',
            path: '/admin/projects',
            permission: 'admin.projects'
          }
        ]
      }
    ];

    // Filter menu items based on user permissions
    const filterMenuItems = (items) => {
      return items.filter(item => {
        if (!hasPermission(user?.role, item.permission)) {
          return false;
        }
        
        if (item.children) {
          item.children = filterMenuItems(item.children);
          return item.children.length > 0;
        }
        
        return true;
      });
    };

    return filterMenuItems(baseMenu);
  }, [user?.role]);
};
```

## Routing Strategy

### Protected Routes

**Route Protection Component:**
```typescript
const ProtectedRoute = ({ 
  children, 
  requiredRole, 
  requiredPermission,
  fallback = <Navigate to="/unauthorized" replace />
}) => {
  const { user, isAuthenticated } = useAuth();
  
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }
  
  if (requiredRole && !hasAnyRole(user?.role, [requiredRole])) {
    return fallback;
  }
  
  if (requiredPermission && !hasPermission(user?.role, requiredPermission)) {
    return fallback;
  }
  
  return children;
};
```

**Route Configuration:**
```typescript
const AppRoutes = () => {
  return (
    <Routes>
      {/* Public Routes */}
      <Route path="/login" element={<LoginPage />} />
      <Route path="/unauthorized" element={<UnauthorizedPage />} />
      
      {/* Protected Routes */}
      <Route path="/" element={
        <ProtectedRoute>
          <MainLayout />
        </ProtectedRoute>
      }>
        {/* Purchase Orders */}
        <Route index element={<Navigate to="/purchase-orders" replace />} />
        <Route path="purchase-orders" element={
          <ProtectedRoute requiredPermission="purchase-orders">
            <PurchaseOrdersPage />
          </ProtectedRoute>
        } />
        <Route path="purchase-orders/create" element={
          <ProtectedRoute requiredPermission="purchase-orders.create">
            <CreatePOPage />
          </ProtectedRoute>
        } />
        <Route path="purchase-orders/:id" element={
          <ProtectedRoute requiredPermission="purchase-orders">
            <PODetailPage />
          </ProtectedRoute>
        } />
        <Route path="purchase-orders/:id/edit" element={
          <ProtectedRoute requiredPermission="purchase-orders">
            <EditPOPage />
          </ProtectedRoute>
        } />
        
        {/* Approvals */}
        <Route path="approvals" element={
          <ProtectedRoute requiredPermission="approvals">
            <ApprovalsPage />
          </ProtectedRoute>
        } />
        
        {/* Reports */}
        <Route path="reports/*" element={
          <ProtectedRoute requiredPermission="reports">
            <ReportsRoutes />
          </ProtectedRoute>
        } />
        
        {/* Administration */}
        <Route path="admin/*" element={
          <ProtectedRoute requiredPermission="admin">
            <AdminRoutes />
          </ProtectedRoute>
        } />
      </Route>
      
      {/* Catch all */}
      <Route path="*" element={<NotFoundPage />} />
    </Routes>
  );
};
```

## Breadcrumb Navigation

### Dynamic Breadcrumbs

**Breadcrumb Component:**
```typescript
const DynamicBreadcrumbs = () => {
  const location = useLocation();
  const navigate = useNavigate();
  
  const breadcrumbMap = {
    '/purchase-orders': 'Purchase Orders',
    '/purchase-orders/create': 'Create P/O',
    '/purchase-orders/drafts': 'Draft P/Os',
    '/approvals': 'Approvals',
    '/reports': 'Reports',
    '/admin': 'Administration',
    '/admin/users': 'Users',
    '/admin/vendors': 'Vendors',
    '/profile': 'Profile'
  };
  
  const pathnames = location.pathname.split('/').filter(x => x);
  
  const breadcrumbs = pathnames.map((pathname, index) => {
    const routeTo = `/${pathnames.slice(0, index + 1).join('/')}`;
    const isLast = index === pathnames.length - 1;
    
    return {
      label: breadcrumbMap[routeTo] || pathname,
      path: routeTo,
      isLast
    };
  });
  
  if (breadcrumbs.length <= 1) return null;
  
  return (
    <Breadcrumbs
      aria-label="breadcrumb"
      sx={{ mb: 2 }}
      separator={<NavigateNextIcon fontSize="small" />}
    >
      <Link
        component="button"
        variant="body2"
        onClick={() => navigate('/')}
        sx={{ display: 'flex', alignItems: 'center' }}
      >
        <HomeIcon sx={{ mr: 0.5 }} fontSize="inherit" />
        Home
      </Link>
      
      {breadcrumbs.map((crumb, index) => (
        crumb.isLast ? (
          <Typography key={crumb.path} color="text.primary" variant="body2">
            {crumb.label}
          </Typography>
        ) : (
          <Link
            key={crumb.path}
            component="button"
            variant="body2"
            onClick={() => navigate(crumb.path)}
          >
            {crumb.label}
          </Link>
        )
      ))}
    </Breadcrumbs>
  );
};
```

## Context Menu & Quick Actions

### Right-Click Context Menus

**Context Menu Implementation:**
```typescript
const useContextMenu = () => {
  const [contextMenu, setContextMenu] = useState(null);
  
  const handleContextMenu = (event, menuItems) => {
    event.preventDefault();
    setContextMenu(
      contextMenu === null
        ? {
            mouseX: event.clientX - 2,
            mouseY: event.clientY - 4,
            items: menuItems
          }
        : null
    );
  };
  
  const handleClose = () => {
    setContextMenu(null);
  };
  
  const ContextMenuComponent = () => (
    <Menu
      open={contextMenu !== null}
      onClose={handleClose}
      anchorReference="anchorPosition"
      anchorPosition={
        contextMenu !== null
          ? { top: contextMenu.mouseY, left: contextMenu.mouseX }
          : undefined
      }
    >
      {contextMenu?.items.map((item, index) => (
        <MenuItem
          key={index}
          onClick={() => {
            item.action();
            handleClose();
          }}
          disabled={item.disabled}
        >
          <ListItemIcon>{item.icon}</ListItemIcon>
          <ListItemText>{item.label}</ListItemText>
        </MenuItem>
      ))}
    </Menu>
  );
  
  return { handleContextMenu, ContextMenuComponent };
};
```

## Navigation State Management

### Navigation History & State

**Navigation State Hook:**
```typescript
const useNavigationState = () => {
  const [navigationHistory, setNavigationHistory] = useState([]);
  const [currentIndex, setCurrentIndex] = useState(-1);
  const location = useLocation();
  
  useEffect(() => {
    const newEntry = {
      path: location.pathname,
      search: location.search,
      timestamp: Date.now()
    };
    
    setNavigationHistory(prev => {
      const newHistory = [...prev.slice(0, currentIndex + 1), newEntry];
      setCurrentIndex(newHistory.length - 1);
      return newHistory.slice(-50); // Keep last 50 entries
    });
  }, [location]);
  
  const canGoBack = currentIndex > 0;
  const canGoForward = currentIndex < navigationHistory.length - 1;
  
  const goBack = () => {
    if (canGoBack) {
      const prevEntry = navigationHistory[currentIndex - 1];
      navigate(prevEntry.path + prevEntry.search);
    }
  };
  
  const goForward = () => {
    if (canGoForward) {
      const nextEntry = navigationHistory[currentIndex + 1];
      navigate(nextEntry.path + nextEntry.search);
    }
  };
  
  return {
    navigationHistory,
    canGoBack,
    canGoForward,
    goBack,
    goForward
  };
};
```

## Best Practices

### Performance Optimization
- Lazy load navigation components
- Use React.memo for navigation items
- Implement virtual scrolling for large menus
- Cache permission calculations

### Accessibility
- Provide keyboard navigation support
- Use proper ARIA labels and roles
- Ensure focus management
- Support screen readers

### UX Guidelines
- Maintain consistent navigation patterns
- Provide visual feedback for active states
- Use appropriate icons and labels
- Implement smooth transitions

### Mobile Considerations
- Touch-friendly target sizes (44px minimum)
- Swipe gestures for navigation
- Proper handling of device orientation
- Optimize for one-handed use
