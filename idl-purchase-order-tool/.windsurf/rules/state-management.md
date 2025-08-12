---
trigger: always_on
---

# IDL Purchase Order Tool - State Management Patterns

## Overview

This document defines the Redux Toolkit (RTK) state management architecture for the IDL Purchase Order Tool, including store structure, async operations, offline support, and real-time synchronization patterns.

## Store Architecture

### Root State Structure
```typescript
interface RootState {
  auth: AuthState;
  purchaseOrders: PurchaseOrderState;
  forms: FormState;
  ui: UIState;
  cache: CacheState;
  offline: OfflineState;
  realtime: RealtimeState;
}
```

### Auth State
```typescript
interface AuthState {
  user: User | null;
  token: string | null;
  refreshToken: string | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  error: string | null;
  permissions: string[];
  sessionExpiry: Date | null;
}

interface User {
  id: number;
  uuid: string;
  email: string;
  name: string;
  role: UserRole;
  authorizationLimit: number;
  projectIds: string[];
}
```

### Purchase Order State
```typescript
interface PurchaseOrderState {
  // Current user's P/Os
  myPOs: {
    items: PurchaseOrder[];
    loading: boolean;
    error: string | null;
    lastFetch: Date | null;
    hasMore: boolean;
    filters: POFilters;
  };
  
  // P/Os pending approval (for approvers)
  pendingApprovals: {
    items: PendingApproval[];
    loading: boolean;
    error: string | null;
    count: number;
  };
  
  // Currently viewed P/O details
  currentPO: {
    data: PurchaseOrder | null;
    loading: boolean;
    error: string | null;
  };
  
  // P/O creation/editing state
  editor: {
    isCreating: boolean;
    isEditing: boolean;
    currentDraft: PODraft | null;
    validationErrors: Record<string, string>;
    isDirty: boolean;
    lastSaved: Date | null;
  };
}

interface POFilters {
  status: POStatus[];
  dateRange: { start: Date; end: Date } | null;
  projectIds: string[];
  searchTerm: string;
  sortBy: string;
  sortOrder: 'asc' | 'desc';
}
```

### Form State (Multi-Step Forms)
```typescript
interface FormState {
  poCreation: {
    currentStep: number;
    formData: POCreationFormData;
    validation: StepValidation[];
    isDraft: boolean;
    draftId: string | null;
    autoSaveEnabled: boolean;
    lastAutoSave: Date | null;
  };
  
  approval: {
    selectedPOs: number[];
    bulkAction: 'approve' | 'reject' | null;
    comments: string;
    isProcessing: boolean;
  };
}

interface POCreationFormData {
  step1: {
    projectId: string;
    costCode: string;
    poType: 'standard' | 'rolling' | 'walkup';
    ceilingAmount?: number;
    urgencyJustification?: string;
  };
  step2: {
    vendorName: string;
    vendorContact: VendorContact;
    lineItems: POLineItem[];
    deliveryDate?: Date;
    notes?: string;
    attachments: FileAttachment[];
  };
  step3: {
    reviewComplete: boolean;
    termsAccepted: boolean;
    submitComments?: string;
  };
}
```

### UI State
```typescript
interface UIState {
  navigation: {
    sidebarCollapsed: boolean;
    currentPath: string;
    breadcrumbs: Breadcrumb[];
  };
  
  notifications: {
    items: Notification[];
    unreadCount: number;
  };
  
  modals: {
    confirmDialog: ConfirmDialogState | null;
    filePreview: FilePreviewState | null;
    poDetails: PODetailsModalState | null;
  };
  
  loading: {
    global: boolean;
    operations: Record<string, boolean>;
  };
  
  errors: {
    global: string | null;
    fieldErrors: Record<string, string>;
  };
}
```

### Cache State
```typescript
interface CacheState {
  projects: {
    data: Project[];
    lastFetch: Date | null;
    ttl: number;
  };
  
  costCodes: {
    [projectId: string]: {
      data: CostCode[];
      lastFetch: Date | null;
      ttl: number;
    };
  };
  
  vendors: {
    data: Vendor[];
    lastFetch: Date | null;
    ttl: number;
  };
  
  users: {
    data: User[];
    lastFetch: Date | null;
    ttl: number;
  };
}
```

### Offline State
```typescript
interface OfflineState {
  isOnline: boolean;
  pendingActions: OfflineAction[];
  syncInProgress: boolean;
  lastSync: Date | null;
  failedSyncs: FailedSync[];
  draftPOs: PODraft[];
}

interface OfflineAction {
  id: string;
  type: string;
  payload: any;
  timestamp: Date;
  retryCount: number;
  maxRetries: number;
}

interface PODraft {
  id: string;
  formData: POCreationFormData;
  currentStep: number;
  lastModified: Date;
  isOffline: boolean;
}
```

### Realtime State
```typescript
interface RealtimeState {
  connected: boolean;
  connectionId: string | null;
  subscriptions: string[];
  liveUpdates: {
    poStatusChanges: POStatusUpdate[];
    approvalNotifications: ApprovalNotification[];
    systemAlerts: SystemAlert[];
  };
}
```

## RTK Query API Slices

### Purchase Orders API
```typescript
export const purchaseOrdersApi = createApi({
  reducerPath: 'purchaseOrdersApi',
  baseQuery: fetchBaseQuery({
    baseUrl: '/api/v1/purchase-orders',
    prepareHeaders: (headers, { getState }) => {
      const token = (getState() as RootState).auth.token;
      if (token) {
        headers.set('authorization', `Bearer ${token}`);
      }
      return headers;
    },
  }),
  tagTypes: ['PurchaseOrder', 'PendingApproval', 'PODraft'],
  endpoints: (builder) => ({
    // Queries
    getPurchaseOrders: builder.query<PaginatedPOs, POQueryParams>({
      query: (params) => ({
        url: '',
        params: {
          page: params.page,
          limit: params.limit,
          status: params.filters.status,
          projectIds: params.filters.projectIds,
          search: params.filters.searchTerm,
          sortBy: params.filters.sortBy,
          sortOrder: params.filters.sortOrder,
        },
      }),
      providesTags: ['PurchaseOrder'],
      // Optimistic updates for better UX
      onQueryStarted: async (params, { dispatch, queryFulfilled }) => {
        try {
          await queryFulfilled;
        } catch (error) {
          // Handle error, possibly queue for offline retry
          dispatch(addOfflineAction({
            type: 'getPurchaseOrders',
            payload: params,
            timestamp: new Date(),
          }));
        }
      },
    }),

    getPurchaseOrder: builder.query<PurchaseOrder, number>({
      query: (id) => `/${id}`,
      providesTags: (result, error, id) => [{ type: 'PurchaseOrder', id }],
    }),

    getPendingApprovals: builder.query<PendingApproval[], void>({
      query: () => '/pending-approvals',
      providesTags: ['PendingApproval'],
      // Poll for updates every 30 seconds
      pollingInterval: 30000,
    }),

    // Mutations
    createPurchaseOrder: builder.mutation<PurchaseOrder, CreatePORequest>({
      query: (data) => ({
        url: '',
        method: 'POST',
        body: data,
      }),
      invalidatesTags: ['PurchaseOrder', 'PODraft'],
      // Optimistic update
      onQueryStarted: async (data, { dispatch, queryFulfilled }) => {
        const optimisticPO = {
          id: Date.now(), // Temporary ID
          ...data,
          status: 'draft' as POStatus,
          createdAt: new Date(),
        };

        // Add optimistic update to cache
        const patchResult = dispatch(
          purchaseOrdersApi.util.updateQueryData('getPurchaseOrders', {}, (draft) => {
            draft.items.unshift(optimisticPO);
          })
        );

        try {
          await queryFulfilled;
        } catch (error) {
          // Revert optimistic update on failure
          patchResult.undo();
          
          // Queue for offline retry
          dispatch(addOfflineAction({
            type: 'createPurchaseOrder',
            payload: data,
            timestamp: new Date(),
          }));
        }
      },
    }),

    updatePurchaseOrder: builder.mutation<PurchaseOrder, UpdatePORequest>({
      query: ({ id, ...data }) => ({
        url: `/${id}`,
        method: 'PUT',
        body: data,
      }),
      invalidatesTags: (result, error, { id }) => [{ type: 'PurchaseOrder', id }],
    }),

    submitPurchaseOrder: builder.mutation<void, number>({
      query: (id) => ({
        url: `/${id}/submit`,
        method: 'POST',
      }),
      invalidatesTags: ['PurchaseOrder', 'PendingApproval'],
    }),

    approvePurchaseOrders: builder.mutation<void, ApprovalRequest>({
      query: (data) => ({
        url: '/approve',
        method: 'POST',
        body: data,
      }),
      invalidatesTags: ['PurchaseOrder', 'PendingApproval'],
    }),

    // Draft operations
    saveDraft: builder.mutation<PODraft, SaveDraftRequest>({
      query: (data) => ({
        url: '/drafts',
        method: 'POST',
        body: data,
      }),
      invalidatesTags: ['PODraft'],
    }),

    getDrafts: builder.query<PODraft[], void>({
      query: () => '/drafts',
      providesTags: ['PODraft'],
    }),
  }),
});
```

### Cache Management API
```typescript
export const cacheApi = createApi({
  reducerPath: 'cacheApi',
  baseQuery: fetchBaseQuery({
    baseUrl: '/api/v1',
    prepareHeaders: (headers, { getState }) => {
      const token = (getState() as RootState).auth.token;
      if (token) {
        headers.set('authorization', `Bearer ${token}`);
      }
      return headers;
    },
  }),
  tagTypes: ['Project', 'CostCode', 'Vendor', 'User'],
  endpoints: (builder) => ({
    getProjects: builder.query<Project[], void>({
      query: () => '/projects',
      providesTags: ['Project'],
      // Cache for 15 minutes
      keepUnusedDataFor: 15 * 60,
    }),

    getCostCodes: builder.query<CostCode[], string>({
      query: (projectId) => `/projects/${projectId}/cost-codes`,
      providesTags: (result, error, projectId) => [
        { type: 'CostCode', id: projectId }
      ],
      keepUnusedDataFor: 15 * 60,
    }),

    getVendors: builder.query<Vendor[], void>({
      query: () => '/vendors',
      providesTags: ['Vendor'],
      keepUnusedDataFor: 30 * 60, // Cache vendors for 30 minutes
    }),
  }),
});
```

## Async Action Patterns

### Thunk Actions for Complex Operations
```typescript
// Auto-save draft P/O
export const autoSaveDraft = createAsyncThunk(
  'forms/autoSaveDraft',
  async (formData: POCreationFormData, { getState, dispatch }) => {
    const state = getState() as RootState;
    const { draftId, isDraft } = state.forms.poCreation;

    if (!isDraft) return;

    try {
      const result = await dispatch(
        purchaseOrdersApi.endpoints.saveDraft.initiate({
          id: draftId,
          formData,
          currentStep: state.forms.poCreation.currentStep,
        })
      ).unwrap();

      return result;
    } catch (error) {
      // Save to local storage if API fails
      localStorage.setItem(`draft_${draftId}`, JSON.stringify({
        formData,
        currentStep: state.forms.poCreation.currentStep,
        lastSaved: new Date(),
      }));
      
      throw error;
    }
  }
);

// Bulk approval action
export const bulkApprovePOs = createAsyncThunk(
  'purchaseOrders/bulkApprove',
  async (
    { poIds, comments }: { poIds: number[]; comments?: string },
    { dispatch, rejectWithValue }
  ) => {
    try {
      await dispatch(
        purchaseOrdersApi.endpoints.approvePurchaseOrders.initiate({
          poIds,
          comments,
          action: 'approve',
        })
      ).unwrap();

      // Show success notification
      dispatch(addNotification({
        type: 'success',
        message: `Successfully approved ${poIds.length} purchase orders`,
      }));

      return poIds;
    } catch (error) {
      return rejectWithValue(error.message);
    }
  }
);
```

## Offline Support Implementation

### Offline Actions Queue
```typescript
const offlineSlice = createSlice({
  name: 'offline',
  initialState: {
    isOnline: navigator.onLine,
    pendingActions: [],
    syncInProgress: false,
    lastSync: null,
    failedSyncs: [],
    draftPOs: [],
  } as OfflineState,
  reducers: {
    setOnlineStatus: (state, action) => {
      state.isOnline = action.payload;
    },
    
    addOfflineAction: (state, action) => {
      const offlineAction: OfflineAction = {
        id: nanoid(),
        ...action.payload,
        retryCount: 0,
        maxRetries: 3,
      };
      state.pendingActions.push(offlineAction);
    },
    
    removeOfflineAction: (state, action) => {
      state.pendingActions = state.pendingActions.filter(
        action => action.id !== action.payload
      );
    },
    
    saveDraftOffline: (state, action) => {
      const draft: PODraft = {
        id: action.payload.id || nanoid(),
        ...action.payload,
        isOffline: true,
        lastModified: new Date(),
      };
      
      const existingIndex = state.draftPOs.findIndex(d => d.id === draft.id);
      if (existingIndex >= 0) {
        state.draftPOs[existingIndex] = draft;
      } else {
        state.draftPOs.push(draft);
      }
    },
  },
});

// Sync offline actions when back online
export const syncOfflineActions = createAsyncThunk(
  'offline/syncActions',
  async (_, { getState, dispatch }) => {
    const state = getState() as RootState;
    const { pendingActions } = state.offline;

    if (!state.offline.isOnline || pendingActions.length === 0) {
      return;
    }

    dispatch(offlineSlice.actions.setSyncInProgress(true));

    for (const action of pendingActions) {
      try {
        // Replay the action
        await dispatch({ type: action.type, payload: action.payload });
        dispatch(offlineSlice.actions.removeOfflineAction(action.id));
      } catch (error) {
        // Increment retry count
        const updatedAction = {
          ...action,
          retryCount: action.retryCount + 1,
        };

        if (updatedAction.retryCount >= updatedAction.maxRetries) {
          // Move to failed syncs
          dispatch(offlineSlice.actions.addFailedSync({
            action: updatedAction,
            error: error.message,
            timestamp: new Date(),
          }));
          dispatch(offlineSlice.actions.removeOfflineAction(action.id));
        }
      }
    }

    dispatch(offlineSlice.actions.setSyncInProgress(false));
    dispatch(offlineSlice.actions.setLastSync(new Date()));
  }
);
```

## Real-time Updates

### WebSocket Integration
```typescript
const realtimeSlice = createSlice({
  name: 'realtime',
  initialState: {
    connected: false,
    connectionId: null,
    subscriptions: [],
    liveUpdates: {
      poStatusChanges: [],
      approvalNotifications: [],
      systemAlerts: [],
    },
  } as RealtimeState,
  reducers: {
    setConnectionStatus: (state, action) => {
      state.connected = action.payload.connected;
      state.connectionId = action.payload.connectionId;
    },
    
    addSubscription: (state, action) => {
      if (!state.subscriptions.includes(action.payload)) {
        state.subscriptions.push(action.payload);
      }
    },
    
    handlePOStatusUpdate: (state, action) => {
      state.liveUpdates.poStatusChanges.unshift(action.payload);
      // Keep only last 50 updates
      state.liveUpdates.poStatusChanges = state.liveUpdates.poStatusChanges.slice(0, 50);
    },
    
    handleApprovalNotification: (state, action) => {
      state.liveUpdates.approvalNotifications.unshift(action.payload);
      state.liveUpdates.approvalNotifications = state.liveUpdates.approvalNotifications.slice(0, 20);
    },
  },
});

// WebSocket middleware
export const websocketMiddleware: Middleware = (store) => (next) => (action) => {
  if (action.type === 'realtime/connect') {
    const ws = new WebSocket(process.env.REACT_APP_WS_URL!);
    
    ws.onopen = () => {
      store.dispatch(realtimeSlice.actions.setConnectionStatus({
        connected: true,
        connectionId: nanoid(),
      }));
    };
    
    ws.onmessage = (event) => {
      const message = JSON.parse(event.data);
      
      switch (message.type) {
        case 'po_status_update':
          store.dispatch(realtimeSlice.actions.handlePOStatusUpdate(message.data));
          // Invalidate relevant RTK Query cache
          store.dispatch(purchaseOrdersApi.util.invalidateTags(['PurchaseOrder']));
          break;
          
        case 'approval_notification':
          store.dispatch(realtimeSlice.actions.handleApprovalNotification(message.data));
          store.dispatch(purchaseOrdersApi.util.invalidateTags(['PendingApproval']));
          break;
      }
    };
    
    ws.onclose = () => {
      store.dispatch(realtimeSlice.actions.setConnectionStatus({
        connected: false,
        connectionId: null,
      }));
    };
  }
  
  return next(action);
};
```

## Store Configuration

### Store Setup
```typescript
export const store = configureStore({
  reducer: {
    auth: authSlice.reducer,
    purchaseOrders: purchaseOrderSlice.reducer,
    forms: formSlice.reducer,
    ui: uiSlice.reducer,
    cache: cacheSlice.reducer,
    offline: offlineSlice.reducer,
    realtime: realtimeSlice.reducer,
    
    // RTK Query APIs
    [purchaseOrdersApi.reducerPath]: purchaseOrdersApi.reducer,
    [cacheApi.reducerPath]: cacheApi.reducer,
  },
  
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware({
      serializableCheck: {
        ignoredActions: [FLUSH, REHYDRATE, PAUSE, PERSIST, PURGE, REGISTER],
      },
    })
    .concat(purchaseOrdersApi.middleware)
    .concat(cacheApi.middleware)
    .concat(websocketMiddleware)
    .concat(offlineMiddleware),
    
  devTools: process.env.NODE_ENV !== 'production',
});

// Persistence configuration
const persistConfig = {
  key: 'root',
  storage,
  whitelist: ['auth', 'offline', 'forms'], // Only persist essential state
  blacklist: ['purchaseOrdersApi', 'cacheApi'], // Don't persist API cache
};

export const persistedReducer = persistReducer(persistConfig, store.reducer);
```

### Custom Hooks
```typescript
// Typed hooks for better TypeScript support
export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;

// Custom hooks for common operations
export const usePOCreationForm = () => {
  const dispatch = useAppDispatch();
  const formState = useAppSelector(state => state.forms.poCreation);
  
  const updateFormData = useCallback((step: number, data: any) => {
    dispatch(updatePOFormData({ step, data }));
    
    // Auto-save after 2 seconds of inactivity
    const autoSaveTimer = setTimeout(() => {
      dispatch(autoSaveDraft(formState.formData));
    }, 2000);
    
    return () => clearTimeout(autoSaveTimer);
  }, [dispatch, formState.formData]);
  
  return {
    ...formState,
    updateFormData,
  };
};

export const useOfflineStatus = () => {
  const dispatch = useAppDispatch();
  const { isOnline, pendingActions, syncInProgress } = useAppSelector(state => state.offline);
  
  useEffect(() => {
    const handleOnline = () => {
      dispatch(offlineSlice.actions.setOnlineStatus(true));
      dispatch(syncOfflineActions());
    };
    
    const handleOffline = () => {
      dispatch(offlineSlice.actions.setOnlineStatus(false));
    };
    
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, [dispatch]);
  
  return {
    isOnline,
    pendingActionsCount: pendingActions.length,
    syncInProgress,
  };
};
```

---

*This state management architecture provides a robust foundation for the IDL Purchase Order Tool, supporting offline capabilities, real-time updates, and optimistic UI updates while maintaining data consistency and user experience.*
