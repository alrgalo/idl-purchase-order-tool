---
trigger: always_on
---

# IDL Purchase Order Tool - Material-UI Theme Configuration

## Overview

This document provides comprehensive Material-UI theme configuration for the IDL Purchase Order Tool, ensuring consistent design tokens, component styling, and responsive behavior across the application. The theme is designed to support business application requirements while maintaining accessibility and mobile-first design principles.

## Theme Architecture

### Core Theme Structure
```typescript
import { createTheme, ThemeOptions } from '@mui/material/styles';
import { PaletteOptions, TypographyOptions, ComponentsOverrides } from '@mui/material/styles';

interface IDLThemeOptions extends ThemeOptions {
  palette: IDLPaletteOptions;
  typography: IDLTypographyOptions;
  spacing: number;
  shape: { borderRadius: number };
  components: ComponentsOverrides;
  breakpoints: BreakpointsOptions;
  zIndex: ZIndexOptions;
}
```

## Color Palette

### Primary Colors
```typescript
const palette: PaletteOptions = {
  primary: {
    main: '#1976d2',        // IDL Blue - Primary actions, links
    light: '#42a5f5',       // Hover states, light backgrounds
    dark: '#1565c0',        // Active states, emphasis
    contrastText: '#ffffff'
  },
  secondary: {
    main: '#dc004e',        // Accent color - Secondary actions
    light: '#ff5983',       // Light accent backgrounds
    dark: '#9a0036',        // Dark accent states
    contrastText: '#ffffff'
  },
  success: {
    main: '#2e7d32',        // Approved status, success messages
    light: '#4caf50',       // Success backgrounds
    dark: '#1b5e20',        // Success emphasis
    contrastText: '#ffffff'
  },
  warning: {
    main: '#ed6c02',        // Pending status, warnings
    light: '#ff9800',       // Warning backgrounds
    dark: '#e65100',        // Warning emphasis
    contrastText: '#ffffff'
  },
  error: {
    main: '#d32f2f',        // Rejected status, errors
    light: '#f44336',       // Error backgrounds
    dark: '#c62828',        // Error emphasis
    contrastText: '#ffffff'
  },
  info: {
    main: '#0288d1',        // Information messages, hints
    light: '#03a9f4',       // Info backgrounds
    dark: '#01579b',        // Info emphasis
    contrastText: '#ffffff'
  }
};
```

### Status Colors
```typescript
const statusColors = {
  draft: '#9e9e9e',         // Draft P/O status
  submitted: '#ff9800',     // Submitted for approval
  approved: '#4caf50',      // Approved P/O
  rejected: '#f44336',      // Rejected P/O
  cancelled: '#9e9e9e',     // Cancelled P/O
  closed: '#607d8b',        // Closed/completed P/O
  
  // Role-based colors
  foreman: '#795548',       // Foreman role indicator
  super: '#3f51b5',         // Superintendent role
  pm: '#673ab7',            // Project Manager role
  admin: '#e91e63'          // Administrator role
};
```

### Neutral Colors
```typescript
const neutrals = {
  grey: {
    50: '#fafafa',          // Light backgrounds
    100: '#f5f5f5',         // Card backgrounds
    200: '#eeeeee',         // Dividers, borders
    300: '#e0e0e0',         // Disabled backgrounds
    400: '#bdbdbd',         // Placeholder text
    500: '#9e9e9e',         // Secondary text
    600: '#757575',         // Primary text on light
    700: '#616161',         // Headers, emphasis
    800: '#424242',         // Dark text
    900: '#212121'          // Highest contrast text
  },
  common: {
    black: '#000000',
    white: '#ffffff'
  }
};
```

## Typography

### Font Configuration
```typescript
const typography: TypographyOptions = {
  fontFamily: [
    'Roboto',
    '-apple-system',
    'BlinkMacSystemFont',
    '"Segoe UI"',
    '"Helvetica Neue"',
    'Arial',
    'sans-serif'
  ].join(','),
  
  // Font weights
  fontWeightLight: 300,
  fontWeightRegular: 400,
  fontWeightMedium: 500,
  fontWeightBold: 700,
  
  // Base font size (16px)
  fontSize: 16,
  htmlFontSize: 16,
  
  // Typography variants
  h1: {
    fontSize: '2.5rem',     // 40px
    fontWeight: 500,
    lineHeight: 1.2,
    letterSpacing: '-0.01562em',
    marginBottom: '1rem'
  },
  h2: {
    fontSize: '2rem',       // 32px
    fontWeight: 500,
    lineHeight: 1.25,
    letterSpacing: '-0.00833em',
    marginBottom: '0.875rem'
  },
  h3: {
    fontSize: '1.75rem',    // 28px
    fontWeight: 500,
    lineHeight: 1.3,
    letterSpacing: '0em',
    marginBottom: '0.75rem'
  },
  h4: {
    fontSize: '1.5rem',     // 24px
    fontWeight: 500,
    lineHeight: 1.35,
    letterSpacing: '0.00735em',
    marginBottom: '0.625rem'
  },
  h5: {
    fontSize: '1.25rem',    // 20px
    fontWeight: 500,
    lineHeight: 1.4,
    letterSpacing: '0em',
    marginBottom: '0.5rem'
  },
  h6: {
    fontSize: '1.125rem',   // 18px
    fontWeight: 500,
    lineHeight: 1.45,
    letterSpacing: '0.0075em',
    marginBottom: '0.5rem'
  },
  subtitle1: {
    fontSize: '1rem',       // 16px
    fontWeight: 500,
    lineHeight: 1.5,
    letterSpacing: '0.00938em'
  },
  subtitle2: {
    fontSize: '0.875rem',   // 14px
    fontWeight: 500,
    lineHeight: 1.57,
    letterSpacing: '0.00714em'
  },
  body1: {
    fontSize: '1rem',       // 16px
    fontWeight: 400,
    lineHeight: 1.5,
    letterSpacing: '0.00938em'
  },
  body2: {
    fontSize: '0.875rem',   // 14px
    fontWeight: 400,
    lineHeight: 1.57,
    letterSpacing: '0.00714em'
  },
  button: {
    fontSize: '0.875rem',   // 14px
    fontWeight: 500,
    lineHeight: 1.43,
    letterSpacing: '0.02857em',
    textTransform: 'none'   // Override MUI default uppercase
  },
  caption: {
    fontSize: '0.75rem',    // 12px
    fontWeight: 400,
    lineHeight: 1.66,
    letterSpacing: '0.03333em'
  },
  overline: {
    fontSize: '0.625rem',   // 10px
    fontWeight: 400,
    lineHeight: 2.66,
    letterSpacing: '0.08333em',
    textTransform: 'uppercase'
  }
};
```

## Spacing & Layout

### Spacing System
```typescript
const spacing = 8; // Base unit: 8px

// Spacing scale (multiples of 8px)
const spacingScale = {
  xs: 0.25,  // 2px
  sm: 0.5,   // 4px
  md: 1,     // 8px
  lg: 1.5,   // 12px
  xl: 2,     // 16px
  xxl: 3,    // 24px
  xxxl: 4,   // 32px
  xxxxl: 6   // 48px
};
```

### Shape Configuration
```typescript
const shape = {
  borderRadius: 8  // 8px border radius for cards, buttons, inputs
};
```

## Breakpoints

### Responsive Breakpoints
```typescript
const breakpoints = {
  values: {
    xs: 0,      // Mobile portrait
    sm: 600,    // Mobile landscape / small tablet
    md: 900,    // Tablet portrait
    lg: 1200,   // Desktop / tablet landscape
    xl: 1536    // Large desktop
  }
};
```

### Container Max Widths
```typescript
const containerMaxWidths = {
  xs: '100%',
  sm: '600px',
  md: '900px',
  lg: '1200px',
  xl: '1536px'
};
```

## Component Overrides

### Button Components
```typescript
const MuiButton = {
  styleOverrides: {
    root: {
      textTransform: 'none',
      fontWeight: 500,
      borderRadius: 8,
      padding: '8px 16px',
      minHeight: 44, // Touch-friendly minimum
      '&:focus-visible': {
        outline: '2px solid #1976d2',
        outlineOffset: '2px'
      }
    },
    containedPrimary: {
      backgroundColor: '#1976d2',
      '&:hover': {
        backgroundColor: '#1565c0',
        boxShadow: '0 4px 8px rgba(25, 118, 210, 0.3)'
      },
      '&:active': {
        backgroundColor: '#0d47a1'
      }
    },
    containedSecondary: {
      backgroundColor: '#dc004e',
      '&:hover': {
        backgroundColor: '#9a0036',
        boxShadow: '0 4px 8px rgba(220, 0, 78, 0.3)'
      }
    },
    outlined: {
      borderWidth: 2,
      '&:hover': {
        borderWidth: 2,
        backgroundColor: 'rgba(25, 118, 210, 0.04)'
      }
    },
    sizeSmall: {
      padding: '6px 12px',
      fontSize: '0.8125rem'
    },
    sizeLarge: {
      padding: '12px 24px',
      fontSize: '0.9375rem',
      minHeight: 48
    }
  }
};
```

### Input Components
```typescript
const MuiTextField = {
  styleOverrides: {
    root: {
      '& .MuiOutlinedInput-root': {
        borderRadius: 8,
        '&:hover .MuiOutlinedInput-notchedOutline': {
          borderColor: '#1976d2'
        },
        '&.Mui-focused .MuiOutlinedInput-notchedOutline': {
          borderWidth: 2,
          borderColor: '#1976d2'
        },
        '&.Mui-error .MuiOutlinedInput-notchedOutline': {
          borderColor: '#d32f2f'
        }
      },
      '& .MuiInputLabel-root': {
        '&.Mui-focused': {
          color: '#1976d2'
        },
        '&.Mui-error': {
          color: '#d32f2f'
        }
      }
    }
  }
};

const MuiFormHelperText = {
  styleOverrides: {
    root: {
      marginTop: 6,
      fontSize: '0.75rem',
      '&.Mui-error': {
        color: '#d32f2f'
      }
    }
  }
};
```

### Card Components
```typescript
const MuiCard = {
  styleOverrides: {
    root: {
      borderRadius: 12,
      boxShadow: '0 2px 8px rgba(0, 0, 0, 0.1)',
      border: '1px solid #e0e0e0',
      '&:hover': {
        boxShadow: '0 4px 16px rgba(0, 0, 0, 0.12)'
      }
    }
  }
};

const MuiCardContent = {
  styleOverrides: {
    root: {
      padding: 24,
      '&:last-child': {
        paddingBottom: 24
      }
    }
  }
};
```

### Data Display Components
```typescript
const MuiChip = {
  styleOverrides: {
    root: {
      borderRadius: 16,
      fontWeight: 500,
      fontSize: '0.8125rem'
    },
    colorPrimary: {
      backgroundColor: '#e3f2fd',
      color: '#1565c0'
    },
    colorSecondary: {
      backgroundColor: '#fce4ec',
      color: '#9a0036'
    }
  }
};

const MuiTableCell = {
  styleOverrides: {
    root: {
      borderBottom: '1px solid #e0e0e0',
      padding: '12px 16px'
    },
    head: {
      backgroundColor: '#f5f5f5',
      fontWeight: 600,
      color: '#424242'
    }
  }
};
```

### Navigation Components
```typescript
const MuiAppBar = {
  styleOverrides: {
    root: {
      backgroundColor: '#ffffff',
      color: '#424242',
      boxShadow: '0 1px 3px rgba(0, 0, 0, 0.12)',
      borderBottom: '1px solid #e0e0e0'
    }
  }
};

const MuiDrawer = {
  styleOverrides: {
    paper: {
      backgroundColor: '#fafafa',
      borderRight: '1px solid #e0e0e0'
    }
  }
};

const MuiListItemButton = {
  styleOverrides: {
    root: {
      borderRadius: 8,
      margin: '2px 8px',
      '&:hover': {
        backgroundColor: 'rgba(25, 118, 210, 0.04)'
      },
      '&.Mui-selected': {
        backgroundColor: 'rgba(25, 118, 210, 0.08)',
        '&:hover': {
          backgroundColor: 'rgba(25, 118, 210, 0.12)'
        }
      }
    }
  }
};
```

## Z-Index Configuration

```typescript
const zIndex = {
  mobileStepper: 1000,
  fab: 1050,
  speedDial: 1050,
  appBar: 1100,
  drawer: 1200,
  modal: 1300,
  snackbar: 1400,
  tooltip: 1500
};
```

## Complete Theme Configuration

### Main Theme Export
```typescript
// src/theme/theme.ts
import { createTheme } from '@mui/material/styles';

export const idlTheme = createTheme({
  palette: {
    primary: {
      main: '#1976d2',
      light: '#42a5f5',
      dark: '#1565c0',
      contrastText: '#ffffff'
    },
    secondary: {
      main: '#dc004e',
      light: '#ff5983',
      dark: '#9a0036',
      contrastText: '#ffffff'
    },
    success: {
      main: '#2e7d32',
      light: '#4caf50',
      dark: '#1b5e20',
      contrastText: '#ffffff'
    },
    warning: {
      main: '#ed6c02',
      light: '#ff9800',
      dark: '#e65100',
      contrastText: '#ffffff'
    },
    error: {
      main: '#d32f2f',
      light: '#f44336',
      dark: '#c62828',
      contrastText: '#ffffff'
    },
    info: {
      main: '#0288d1',
      light: '#03a9f4',
      dark: '#01579b',
      contrastText: '#ffffff'
    },
    grey: {
      50: '#fafafa',
      100: '#f5f5f5',
      200: '#eeeeee',
      300: '#e0e0e0',
      400: '#bdbdbd',
      500: '#9e9e9e',
      600: '#757575',
      700: '#616161',
      800: '#424242',
      900: '#212121'
    }
  },
  typography: {
    fontFamily: [
      'Roboto',
      '-apple-system',
      'BlinkMacSystemFont',
      '"Segoe UI"',
      '"Helvetica Neue"',
      'Arial',
      'sans-serif'
    ].join(','),
    h1: {
      fontSize: '2.5rem',
      fontWeight: 500,
      lineHeight: 1.2
    },
    h2: {
      fontSize: '2rem',
      fontWeight: 500,
      lineHeight: 1.25
    },
    h3: {
      fontSize: '1.75rem',
      fontWeight: 500,
      lineHeight: 1.3
    },
    h4: {
      fontSize: '1.5rem',
      fontWeight: 500,
      lineHeight: 1.35
    },
    h5: {
      fontSize: '1.25rem',
      fontWeight: 500,
      lineHeight: 1.4
    },
    h6: {
      fontSize: '1.125rem',
      fontWeight: 500,
      lineHeight: 1.45
    },
    button: {
      textTransform: 'none',
      fontWeight: 500
    }
  },
  spacing: 8,
  shape: {
    borderRadius: 8
  },
  breakpoints: {
    values: {
      xs: 0,
      sm: 600,
      md: 900,
      lg: 1200,
      xl: 1536
    }
  },
  components: {
    MuiButton: {
      styleOverrides: {
        root: {
          textTransform: 'none',
          borderRadius: 8,
          minHeight: 44,
          '&:focus-visible': {
            outline: '2px solid #1976d2',
            outlineOffset: '2px'
          }
        }
      }
    },
    MuiTextField: {
      styleOverrides: {
        root: {
          '& .MuiOutlinedInput-root': {
            borderRadius: 8
          }
        }
      }
    },
    MuiCard: {
      styleOverrides: {
        root: {
          borderRadius: 12,
          boxShadow: '0 2px 8px rgba(0, 0, 0, 0.1)',
          border: '1px solid #e0e0e0'
        }
      }
    },
    MuiChip: {
      styleOverrides: {
        root: {
          borderRadius: 16,
          fontWeight: 500
        }
      }
    }
  }
});
```

## Status-Specific Theme Utilities

### Status Color Helper
```typescript
// src/theme/status-colors.ts
export const getStatusColor = (status: string): string => {
  const statusColorMap: Record<string, string> = {
    draft: '#9e9e9e',
    submitted: '#ff9800',
    approved: '#4caf50',
    rejected: '#f44336',
    cancelled: '#9e9e9e',
    closed: '#607d8b'
  };
  
  return statusColorMap[status] || '#9e9e9e';
};

export const getRoleColor = (role: string): string => {
  const roleColorMap: Record<string, string> = {
    foreman: '#795548',
    super: '#3f51b5',
    pm: '#673ab7',
    admin: '#e91e63'
  };
  
  return roleColorMap[role] || '#9e9e9e';
};
```

### Custom Theme Hooks
```typescript
// src/theme/theme-hooks.ts
import { useTheme } from '@mui/material/styles';
import { useMediaQuery } from '@mui/material';

export const useResponsive = () => {
  const theme = useTheme();
  
  return {
    isMobile: useMediaQuery(theme.breakpoints.down('sm')),
    isTablet: useMediaQuery(theme.breakpoints.between('sm', 'lg')),
    isDesktop: useMediaQuery(theme.breakpoints.up('lg')),
    isSmallScreen: useMediaQuery(theme.breakpoints.down('md'))
  };
};

export const useStatusTheme = () => {
  const theme = useTheme();
  
  return {
    getStatusChipProps: (status: string) => ({
      sx: {
        backgroundColor: `${getStatusColor(status)}20`,
        color: getStatusColor(status),
        fontWeight: 500
      }
    }),
    getRoleChipProps: (role: string) => ({
      sx: {
        backgroundColor: `${getRoleColor(role)}20`,
        color: getRoleColor(role),
        fontWeight: 500
      }
    })
  };
};
```

## Dark Mode Support (Future Enhancement)

### Dark Theme Configuration
```typescript
// src/theme/dark-theme.ts
export const darkTheme = createTheme({
  palette: {
    mode: 'dark',
    primary: {
      main: '#90caf9',
      light: '#e3f2fd',
      dark: '#42a5f5'
    },
    background: {
      default: '#121212',
      paper: '#1e1e1e'
    },
    text: {
      primary: '#ffffff',
      secondary: 'rgba(255, 255, 255, 0.7)'
    }
  }
  // ... rest of dark theme configuration
});
```

## Implementation Guidelines

### Theme Provider Setup
```typescript
// src/App.tsx
import { ThemeProvider } from '@mui/material/styles';
import CssBaseline from '@mui/material/CssBaseline';
import { idlTheme } from './theme/theme';

function App() {
  return (
    <ThemeProvider theme={idlTheme}>
      <CssBaseline />
      {/* App content */}
    </ThemeProvider>
  );
}
```

### Using Theme in Components
```typescript
// Component example
import { useTheme } from '@mui/material/styles';
import { Box, Typography } from '@mui/material';

function StatusBadge({ status }: { status: string }) {
  const theme = useTheme();
  
  return (
    <Box
      sx={{
        backgroundColor: getStatusColor(status),
        color: theme.palette.common.white,
        padding: theme.spacing(0.5, 1),
        borderRadius: theme.shape.borderRadius / 2,
        fontSize: theme.typography.caption.fontSize,
        fontWeight: theme.typography.fontWeightMedium
      }}
    >
      {status.toUpperCase()}
    </Box>
  );
}
```

## Accessibility Considerations

### Focus Management
```typescript
const focusStyles = {
  '&:focus-visible': {
    outline: '2px solid #1976d2',
    outlineOffset: '2px',
    borderRadius: 4
  }
};
```

### Color Contrast
- All color combinations meet WCAG AA standards (4.5:1 ratio minimum)
- Status colors provide sufficient contrast against white backgrounds
- Interactive elements have clear focus indicators

### Typography Scale
- Minimum font size: 12px (0.75rem) for captions
- Body text: 16px (1rem) for optimal readability
- Touch targets: Minimum 44px height for interactive elements

This Material-UI theme configuration provides a comprehensive foundation for the IDL Purchase Order Tool, ensuring consistency, accessibility, and maintainability across all components and pages.
