---
trigger: always_on
---

# Mobile Patterns Guide

## Overview

This document outlines mobile-specific patterns and implementations for the IDL Purchase Order Tool, including camera integration, offline functionality, and mobile navigation patterns.

## Camera Integration

### Device Camera Access

**Implementation Pattern:**
```typescript
// Camera access hook
const useCameraCapture = () => {
  const [isSupported, setIsSupported] = useState(false);
  const [stream, setStream] = useState<MediaStream | null>(null);

  const startCamera = async () => {
    try {
      const mediaStream = await navigator.mediaDevices.getUserMedia({
        video: { facingMode: 'environment' }, // Back camera preferred
        audio: false
      });
      setStream(mediaStream);
      return mediaStream;
    } catch (error) {
      console.error('Camera access denied:', error);
      throw error;
    }
  };

  const capturePhoto = (videoElement: HTMLVideoElement) => {
    const canvas = document.createElement('canvas');
    canvas.width = videoElement.videoWidth;
    canvas.height = videoElement.videoHeight;
    const ctx = canvas.getContext('2d');
    ctx?.drawImage(videoElement, 0, 0);
    return canvas.toDataURL('image/jpeg', 0.8);
  };

  return { isSupported, startCamera, capturePhoto, stream };
};
```

**Usage in Components:**
```typescript
// In POAttachments component
const CameraCapture = ({ onCapture, onCancel }) => {
  const { startCamera, capturePhoto } = useCameraCapture();
  const videoRef = useRef<HTMLVideoElement>(null);

  const handleCapture = () => {
    if (videoRef.current) {
      const photoData = capturePhoto(videoRef.current);
      onCapture(photoData);
    }
  };

  return (
    <Box sx={{ position: 'relative', width: '100%', height: '400px' }}>
      <video
        ref={videoRef}
        autoPlay
        playsInline
        style={{ width: '100%', height: '100%', objectFit: 'cover' }}
      />
      <Box sx={{ position: 'absolute', bottom: 16, left: '50%', transform: 'translateX(-50%)' }}>
        <IconButton onClick={handleCapture} size="large" color="primary">
          <CameraAltIcon />
        </IconButton>
        <IconButton onClick={onCancel} size="large" color="secondary">
          <CloseIcon />
        </IconButton>
      </Box>
    </Box>
  );
};
```

### File Processing

**Image Compression:**
```typescript
const compressImage = (file: File, maxWidth = 1920, quality = 0.8): Promise<File> => {
  return new Promise((resolve) => {
    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d');
    const img = new Image();

    img.onload = () => {
      const ratio = Math.min(maxWidth / img.width, maxWidth / img.height);
      canvas.width = img.width * ratio;
      canvas.height = img.height * ratio;

      ctx?.drawImage(img, 0, 0, canvas.width, canvas.height);
      
      canvas.toBlob((blob) => {
        const compressedFile = new File([blob!], file.name, {
          type: 'image/jpeg',
          lastModified: Date.now()
        });
        resolve(compressedFile);
      }, 'image/jpeg', quality);
    };

    img.src = URL.createObjectURL(file);
  });
};
```

## Offline Functionality

### Draft P/O Storage

**Local Storage Pattern:**
```typescript
// Offline storage service
class OfflineStorageService {
  private readonly DRAFT_KEY = 'po_drafts';
  private readonly SYNC_QUEUE_KEY = 'sync_queue';

  saveDraft(poData: Partial<PurchaseOrder>): string {
    const drafts = this.getDrafts();
    const draftId = `draft_${Date.now()}`;
    
    drafts[draftId] = {
      ...poData,
      id: draftId,
      status: 'draft',
      last_modified: new Date().toISOString(),
      is_offline: true
    };

    localStorage.setItem(this.DRAFT_KEY, JSON.stringify(drafts));
    return draftId;
  }

  getDrafts(): Record<string, Partial<PurchaseOrder>> {
    const stored = localStorage.getItem(this.DRAFT_KEY);
    return stored ? JSON.parse(stored) : {};
  }

  queueForSync(action: 'create' | 'update' | 'delete', data: any): void {
    const queue = this.getSyncQueue();
    queue.push({
      id: `sync_${Date.now()}`,
      action,
      data,
      timestamp: new Date().toISOString(),
      retries: 0
    });
    localStorage.setItem(this.SYNC_QUEUE_KEY, JSON.stringify(queue));
  }

  getSyncQueue(): SyncQueueItem[] {
    const stored = localStorage.getItem(this.SYNC_QUEUE_KEY);
    return stored ? JSON.parse(stored) : [];
  }
}
```

### Auto-Sync Implementation

**Network Status Detection:**
```typescript
const useNetworkStatus = () => {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  const [syncStatus, setSyncStatus] = useState<'idle' | 'syncing' | 'error'>('idle');

  useEffect(() => {
    const handleOnline = () => setIsOnline(true);
    const handleOffline = () => setIsOnline(false);

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);

    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);

  const syncPendingChanges = async () => {
    if (!isOnline) return;

    setSyncStatus('syncing');
    const offlineService = new OfflineStorageService();
    const queue = offlineService.getSyncQueue();

    try {
      for (const item of queue) {
        await syncQueueItem(item);
      }
      // Clear sync queue on success
      localStorage.removeItem('sync_queue');
      setSyncStatus('idle');
    } catch (error) {
      setSyncStatus('error');
      console.error('Sync failed:', error);
    }
  };

  return { isOnline, syncStatus, syncPendingChanges };
};
```

### Offline Indicators

**UI Components:**
```typescript
const OfflineIndicator = () => {
  const { isOnline, syncStatus } = useNetworkStatus();

  if (isOnline && syncStatus === 'idle') return null;

  return (
    <Alert 
      severity={isOnline ? 'info' : 'warning'}
      sx={{ mb: 2 }}
    >
      {!isOnline && 'Working offline - changes will sync when connection is restored'}
      {isOnline && syncStatus === 'syncing' && 'Syncing changes...'}
      {syncStatus === 'error' && 'Sync failed - will retry automatically'}
    </Alert>
  );
};

const DraftBadge = ({ isDraft }: { isDraft: boolean }) => {
  if (!isDraft) return null;

  return (
    <Chip
      label="Draft (Offline)"
      size="small"
      color="warning"
      icon={<CloudOffIcon />}
      sx={{ ml: 1 }}
    />
  );
};
```

## Mobile Navigation

### Bottom Tab Navigation

**Implementation:**
```typescript
const MobileBottomNav = () => {
  const location = useLocation();
  const navigate = useNavigate();
  const { user } = useAuth();

  const tabs = [
    { label: 'P/Os', value: '/', icon: <ReceiptIcon /> },
    { label: 'Create', value: '/create', icon: <AddIcon /> },
    { label: 'Approvals', value: '/approvals', icon: <ApprovalIcon /> },
    { label: 'Profile', value: '/profile', icon: <PersonIcon /> }
  ];

  // Filter tabs based on user permissions
  const visibleTabs = tabs.filter(tab => {
    if (tab.value === '/approvals') {
      return canApprove(user?.role);
    }
    return true;
  });

  return (
    <Paper 
      sx={{ 
        position: 'fixed', 
        bottom: 0, 
        left: 0, 
        right: 0, 
        zIndex: 1000,
        display: { xs: 'block', md: 'none' } // Only show on mobile
      }}
    >
      <BottomNavigation
        value={location.pathname}
        onChange={(_, newValue) => navigate(newValue)}
      >
        {visibleTabs.map(tab => (
          <BottomNavigationAction
            key={tab.value}
            label={tab.label}
            value={tab.value}
            icon={tab.icon}
          />
        ))}
      </BottomNavigation>
    </Paper>
  );
};
```

### Responsive Sidebar

**Desktop/Mobile Adaptive:**
```typescript
const ResponsiveNavigation = () => {
  const theme = useTheme();
  const isMobile = useMediaQuery(theme.breakpoints.down('md'));
  const [mobileOpen, setMobileOpen] = useState(false);

  const handleDrawerToggle = () => {
    setMobileOpen(!mobileOpen);
  };

  const drawer = (
    <Box>
      <Toolbar />
      <Divider />
      <List>
        {navigationItems.map(item => (
          <ListItem key={item.path} disablePadding>
            <ListItemButton component={Link} to={item.path}>
              <ListItemIcon>{item.icon}</ListItemIcon>
              <ListItemText primary={item.label} />
            </ListItemButton>
          </ListItem>
        ))}
      </List>
    </Box>
  );

  return (
    <Box sx={{ display: 'flex' }}>
      {/* Desktop Sidebar */}
      <Drawer
        variant="permanent"
        sx={{
          display: { xs: 'none', md: 'block' },
          '& .MuiDrawer-paper': { width: 240, boxSizing: 'border-box' }
        }}
      >
        {drawer}
      </Drawer>

      {/* Mobile Drawer */}
      <Drawer
        variant="temporary"
        open={mobileOpen}
        onClose={handleDrawerToggle}
        sx={{
          display: { xs: 'block', md: 'none' },
          '& .MuiDrawer-paper': { width: 240, boxSizing: 'border-box' }
        }}
      >
        {drawer}
      </Drawer>

      {/* Mobile Bottom Navigation */}
      {isMobile && <MobileBottomNav />}
    </Box>
  );
};
```

## Touch Interactions

### Swipe Gestures

**Swipe-to-Action Pattern:**
```typescript
const useSwipeActions = (onSwipeLeft?: () => void, onSwipeRight?: () => void) => {
  const [touchStart, setTouchStart] = useState<number | null>(null);
  const [touchEnd, setTouchEnd] = useState<number | null>(null);

  const minSwipeDistance = 50;

  const onTouchStart = (e: TouchEvent) => {
    setTouchEnd(null);
    setTouchStart(e.targetTouches[0].clientX);
  };

  const onTouchMove = (e: TouchEvent) => {
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
```

### Pull-to-Refresh

**Implementation:**
```typescript
const usePullToRefresh = (onRefresh: () => Promise<void>) => {
  const [isRefreshing, setIsRefreshing] = useState(false);
  const [pullDistance, setPullDistance] = useState(0);

  const handlePullToRefresh = async () => {
    setIsRefreshing(true);
    try {
      await onRefresh();
    } finally {
      setIsRefreshing(false);
      setPullDistance(0);
    }
  };

  const touchHandlers = {
    onTouchStart: (e: TouchEvent) => {
      if (window.scrollY === 0) {
        // Only enable pull-to-refresh at top of page
      }
    },
    onTouchMove: (e: TouchEvent) => {
      if (window.scrollY === 0 && e.touches[0].clientY > 0) {
        setPullDistance(Math.min(e.touches[0].clientY, 100));
      }
    },
    onTouchEnd: () => {
      if (pullDistance > 60) {
        handlePullToRefresh();
      } else {
        setPullDistance(0);
      }
    }
  };

  return { isRefreshing, pullDistance, touchHandlers };
};
```

## Performance Optimizations

### Image Lazy Loading

**Intersection Observer Pattern:**
```typescript
const LazyImage = ({ src, alt, ...props }) => {
  const [imageSrc, setImageSrc] = useState('');
  const [imageRef, setImageRef] = useState<HTMLImageElement | null>(null);

  useEffect(() => {
    let observer: IntersectionObserver;
    
    if (imageRef && imageSrc !== src) {
      observer = new IntersectionObserver(
        entries => {
          entries.forEach(entry => {
            if (entry.isIntersecting) {
              setImageSrc(src);
              observer.unobserve(imageRef);
            }
          });
        },
        { threshold: 0.1 }
      );
      observer.observe(imageRef);
    }
    
    return () => {
      if (observer && observer.unobserve) {
        observer.unobserve(imageRef);
      }
    };
  }, [imageRef, imageSrc, src]);

  return (
    <img
      ref={setImageRef}
      src={imageSrc}
      alt={alt}
      {...props}
    />
  );
};
```

### Virtual Scrolling

**For Large Lists:**
```typescript
const VirtualizedPOList = ({ items, itemHeight = 80 }) => {
  const [scrollTop, setScrollTop] = useState(0);
  const [containerHeight, setContainerHeight] = useState(400);
  
  const startIndex = Math.floor(scrollTop / itemHeight);
  const endIndex = Math.min(
    startIndex + Math.ceil(containerHeight / itemHeight) + 1,
    items.length
  );
  
  const visibleItems = items.slice(startIndex, endIndex);
  
  return (
    <Box
      sx={{ height: containerHeight, overflow: 'auto' }}
      onScroll={(e) => setScrollTop(e.target.scrollTop)}
    >
      <Box sx={{ height: items.length * itemHeight, position: 'relative' }}>
        {visibleItems.map((item, index) => (
          <Box
            key={item.po_id}
            sx={{
              position: 'absolute',
              top: (startIndex + index) * itemHeight,
              width: '100%',
              height: itemHeight
            }}
          >
            <POListItem po={item} />
          </Box>
        ))}
      </Box>
    </Box>
  );
};
```

## Accessibility on Mobile

### Touch Target Sizes

**Minimum 44px touch targets:**
```typescript
const TouchFriendlyButton = styled(Button)(({ theme }) => ({
  minHeight: 44,
  minWidth: 44,
  padding: theme.spacing(1.5),
  
  // Increase touch area without visual change
  '&::before': {
    content: '""',
    position: 'absolute',
    top: -8,
    left: -8,
    right: -8,
    bottom: -8,
    zIndex: -1
  }
}));
```

### Voice Input Support

**Speech Recognition Integration:**
```typescript
const useSpeechInput = () => {
  const [isListening, setIsListening] = useState(false);
  const [transcript, setTranscript] = useState('');

  const startListening = () => {
    if ('webkitSpeechRecognition' in window) {
      const recognition = new webkitSpeechRecognition();
      recognition.continuous = false;
      recognition.interimResults = false;
      recognition.lang = 'en-US';

      recognition.onstart = () => setIsListening(true);
      recognition.onend = () => setIsListening(false);
      recognition.onresult = (event) => {
        const result = event.results[0][0].transcript;
        setTranscript(result);
      };

      recognition.start();
    }
  };

  return { isListening, transcript, startListening };
};
```

## Best Practices

### Performance
- Use React.memo for expensive components
- Implement proper image compression before upload
- Use virtual scrolling for long lists
- Cache API responses with service workers

### UX Guidelines
- Provide immediate feedback for all interactions
- Use skeleton loaders during data fetching
- Implement proper error boundaries
- Show clear offline/online status

### Accessibility
- Maintain 44px minimum touch targets
- Support voice input where appropriate
- Provide haptic feedback for important actions
- Ensure proper focus management

### Testing
- Test on actual devices, not just browser dev tools
- Verify camera functionality across different browsers
- Test offline scenarios thoroughly
- Validate touch interactions on various screen sizes
