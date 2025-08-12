---
trigger: always_on
---

# IDL Purchase Order Tool - Form Patterns and Validation

## Overview

This document defines comprehensive form patterns, validation strategies, and user interaction flows for the IDL Purchase Order Tool. It focuses on the multi-step P/O creation workflow and ensures consistent form behavior across all user interfaces.

## Multi-Step Form Architecture

### P/O Creation Wizard Flow

**Step Progression:**
1. **Project & Cost Code Selection** → 2. **Item Entry & Details** → 3. **Review & Submit**

```typescript
interface POCreationSteps {
  PROJECT_SELECTION: 'project_selection';
  ITEM_ENTRY: 'item_entry';
  REVIEW_SUBMIT: 'review_submit';
}

interface StepValidation {
  stepKey: keyof POCreationSteps;
  isValid: boolean;
  errors: Record<string, string>;
  canProceed: boolean;
}
```

### Step 1: Project & Cost Code Selection

**Required Fields:**
- IDL Project Number (dropdown with search)
- Cost Code (dependent on project selection)
- P/O Type (Standard, Rolling, Walk-up)

**Validation Rules:**
```typescript
const projectSelectionValidation = {
  projectId: {
    required: true,
    message: "Project selection is required"
  },
  costCode: {
    required: true,
    dependsOn: 'projectId',
    message: "Cost code is required for the selected project"
  },
  poType: {
    required: true,
    message: "P/O type must be selected"
  },
  // Rolling P/O specific
  ceilingAmount: {
    required: (data) => data.poType === 'rolling',
    min: 0.01,
    message: "Ceiling amount is required for rolling P/Os"
  },
  // Walk-up P/O specific
  urgencyJustification: {
    required: (data) => data.poType === 'walkup',
    minLength: 10,
    message: "Justification required for walk-up P/Os (minimum 10 characters)"
  }
};
```

**Form Behavior:**
- Project dropdown loads with search/filter capability
- Cost codes load automatically when project is selected
- Show loading states during dependent data fetching
- Display project budget information when available
- Auto-save draft every 30 seconds

### Step 2: Item Entry & Details

**Required Fields:**
- Vendor Name and Contact Information
- At least one line item with complete details
- Delivery information

**Line Item Validation:**
```typescript
const lineItemValidation = {
  description: {
    required: true,
    minLength: 3,
    message: "Item description is required (minimum 3 characters)"
  },
  quantity: {
    required: true,
    min: 0.001,
    type: 'number',
    message: "Quantity must be greater than 0"
  },
  unit: {
    required: true,
    message: "Unit of measure is required"
  },
  unitPrice: {
    required: true,
    min: 0,
    type: 'currency',
    message: "Unit price must be $0.00 or greater"
  },
  category: {
    required: true,
    enum: ['LA', 'EQ', 'MA', 'SC'],
    message: "Category selection is required"
  },
  taxCode: {
    required: true,
    enum: ['GST2', 'PST7', 'GP', 'GST_0', 'XMPT'],
    message: "Tax code selection is required"
  }
};
```

**Dynamic Calculations:**
- Line total = quantity × unit price
- Tax amount = line total × tax rate
- P/O subtotal = sum of all line totals
- Total tax = sum of all line tax amounts
- P/O total = subtotal + total tax

### Step 3: Review & Submit

**Authorization Check:**
```typescript
const authorizationValidation = {
  totalAmount: {
    validate: (amount, userRole, authLimit) => {
      if (amount > authLimit) {
        return {
          isValid: false,
          message: `Amount $${amount.toFixed(2)} exceeds your authorization limit of $${authLimit.toFixed(2)}. Supervisor approval required.`,
          requiresEscalation: true
        };
      }
      return { isValid: true };
    }
  }
};
```

**Pre-submission Validation:**
- All required fields completed
- At least one line item exists
- Authorization limits respected
- Vendor information complete
- File attachments validated (if any)

## Form State Management

### Redux Store Structure
```typescript
interface POFormState {
  currentStep: number;
  formData: {
    projectSelection: ProjectSelectionData;
    itemEntry: ItemEntryData;
    reviewSubmit: ReviewSubmitData;
  };
  validation: {
    [stepKey: string]: StepValidation;
  };
  isDraft: boolean;
  lastSaved: Date | null;
  isSubmitting: boolean;
  submitError: string | null;
}

interface ProjectSelectionData {
  projectId: string;
  costCode: string;
  poType: 'standard' | 'rolling' | 'walkup';
  ceilingAmount?: number;
  urgencyJustification?: string;
}

interface ItemEntryData {
  vendorName: string;
  vendorContact: VendorContact;
  lineItems: POLineItem[];
  deliveryDate?: Date;
  notes?: string;
  attachments: FileAttachment[];
}
```

### Auto-Save Implementation
```typescript
const useAutoSave = (formData: POFormState, interval: number = 30000) => {
  const dispatch = useAppDispatch();
  const [lastSaveTime, setLastSaveTime] = useState<Date | null>(null);

  useEffect(() => {
    const autoSaveTimer = setInterval(() => {
      if (formData.isDraft && hasUnsavedChanges(formData, lastSaveTime)) {
        dispatch(saveDraftPO(formData))
          .then(() => setLastSaveTime(new Date()))
          .catch(error => console.error('Auto-save failed:', error));
      }
    }, interval);

    return () => clearInterval(autoSaveTimer);
  }, [formData, lastSaveTime, dispatch, interval]);

  return lastSaveTime;
};
```

## Validation Patterns

### Real-Time Validation
```typescript
const useFieldValidation = (fieldName: string, value: any, rules: ValidationRule[]) => {
  const [error, setError] = useState<string | null>(null);
  const [isValidating, setIsValidating] = useState(false);

  const validateField = useCallback(async (val: any) => {
    setIsValidating(true);
    
    for (const rule of rules) {
      const result = await rule.validate(val);
      if (!result.isValid) {
        setError(result.message);
        setIsValidating(false);
        return false;
      }
    }
    
    setError(null);
    setIsValidating(false);
    return true;
  }, [rules]);

  // Debounced validation
  useEffect(() => {
    const timer = setTimeout(() => {
      if (value !== null && value !== undefined && value !== '') {
        validateField(value);
      }
    }, 300);

    return () => clearTimeout(timer);
  }, [value, validateField]);

  return { error, isValidating, validateField };
};
```

### Cross-Field Validation
```typescript
const useCrossFieldValidation = (formData: any, dependencies: CrossFieldRule[]) => {
  const [errors, setErrors] = useState<Record<string, string>>({});

  useEffect(() => {
    const newErrors: Record<string, string> = {};

    dependencies.forEach(rule => {
      const result = rule.validate(formData);
      if (!result.isValid) {
        rule.fields.forEach(field => {
          newErrors[field] = result.message;
        });
      }
    });

    setErrors(newErrors);
  }, [formData, dependencies]);

  return errors;
};
```

### Async Validation (External API Checks)
```typescript
const useAsyncValidation = (value: string, validator: AsyncValidator) => {
  const [isValid, setIsValid] = useState<boolean | null>(null);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    if (!value) {
      setIsValid(null);
      setError(null);
      return;
    }

    const validateAsync = async () => {
      setIsLoading(true);
      try {
        const result = await validator.validate(value);
        setIsValid(result.isValid);
        setError(result.isValid ? null : result.message);
      } catch (err) {
        setIsValid(false);
        setError('Validation failed. Please try again.');
      } finally {
        setIsLoading(false);
      }
    };

    const timer = setTimeout(validateAsync, 500); // Debounce async calls
    return () => clearTimeout(timer);
  }, [value, validator]);

  return { isValid, isLoading, error };
};
```

## Error Handling and Recovery

### Error Display Patterns
```typescript
interface ErrorDisplayProps {
  error: string;
  severity: 'error' | 'warning' | 'info';
  showIcon?: boolean;
  actionable?: boolean;
  onRetry?: () => void;
  onDismiss?: () => void;
}

const ErrorDisplay: React.FC<ErrorDisplayProps> = ({
  error,
  severity,
  showIcon = true,
  actionable = false,
  onRetry,
  onDismiss
}) => {
  return (
    <Alert 
      severity={severity}
      action={
        actionable && (
          <Box>
            {onRetry && (
              <Button color="inherit" size="small" onClick={onRetry}>
                Retry
              </Button>
            )}
            {onDismiss && (
              <IconButton color="inherit" size="small" onClick={onDismiss}>
                <CloseIcon fontSize="inherit" />
              </IconButton>
            )}
          </Box>
        )
      }
    >
      {error}
    </Alert>
  );
};
```

### Network Error Recovery
```typescript
const useNetworkErrorRecovery = () => {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  const [retryQueue, setRetryQueue] = useState<RetryableAction[]>([]);

  useEffect(() => {
    const handleOnline = () => {
      setIsOnline(true);
      // Process retry queue when back online
      retryQueue.forEach(action => {
        action.retry();
      });
      setRetryQueue([]);
    };

    const handleOffline = () => {
      setIsOnline(false);
    };

    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);

    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, [retryQueue]);

  const addToRetryQueue = (action: RetryableAction) => {
    setRetryQueue(prev => [...prev, action]);
  };

  return { isOnline, addToRetryQueue };
};
```

## Form Navigation and Flow Control

### Step Navigation Logic
```typescript
const useStepNavigation = (totalSteps: number, validation: StepValidation[]) => {
  const [currentStep, setCurrentStep] = useState(0);

  const canGoNext = useMemo(() => {
    return currentStep < totalSteps - 1 && validation[currentStep]?.isValid;
  }, [currentStep, totalSteps, validation]);

  const canGoPrevious = useMemo(() => {
    return currentStep > 0;
  }, [currentStep]);

  const goNext = useCallback(() => {
    if (canGoNext) {
      setCurrentStep(prev => prev + 1);
    }
  }, [canGoNext]);

  const goPrevious = useCallback(() => {
    if (canGoPrevious) {
      setCurrentStep(prev => prev - 1);
    }
  }, [canGoPrevious]);

  const goToStep = useCallback((stepIndex: number) => {
    if (stepIndex >= 0 && stepIndex < totalSteps) {
      // Check if all previous steps are valid
      const canNavigateToStep = validation
        .slice(0, stepIndex)
        .every(step => step.isValid);
      
      if (canNavigateToStep || stepIndex <= currentStep) {
        setCurrentStep(stepIndex);
      }
    }
  }, [totalSteps, validation, currentStep]);

  return {
    currentStep,
    canGoNext,
    canGoPrevious,
    goNext,
    goPrevious,
    goToStep
  };
};
```

### Form Persistence and Recovery
```typescript
const useFormPersistence = (formId: string, formData: any) => {
  const [isLoaded, setIsLoaded] = useState(false);

  // Load saved form data on mount
  useEffect(() => {
    const savedData = localStorage.getItem(`form_${formId}`);
    if (savedData) {
      try {
        const parsedData = JSON.parse(savedData);
        // Dispatch action to restore form state
        dispatch(restoreFormData(parsedData));
      } catch (error) {
        console.error('Failed to restore form data:', error);
      }
    }
    setIsLoaded(true);
  }, [formId]);

  // Save form data to localStorage
  useEffect(() => {
    if (isLoaded && formData) {
      localStorage.setItem(`form_${formId}`, JSON.stringify(formData));
    }
  }, [formId, formData, isLoaded]);

  const clearSavedData = useCallback(() => {
    localStorage.removeItem(`form_${formId}`);
  }, [formId]);

  return { isLoaded, clearSavedData };
};
```

## Mobile-Specific Form Patterns

### Touch-Optimized Inputs
```typescript
const MobileCurrencyInput: React.FC<CurrencyInputProps> = (props) => {
  return (
    <TextField
      {...props}
      inputProps={{
        inputMode: 'decimal',
        pattern: '[0-9]*\.?[0-9]*',
        style: { fontSize: '16px' } // Prevents zoom on iOS
      }}
      InputProps={{
        startAdornment: <InputAdornment position="start">$</InputAdornment>
      }}
    />
  );
};
```

### Camera Integration for File Uploads
```typescript
const useCameraCapture = () => {
  const [isCapturing, setIsCapturing] = useState(false);
  const [capturedImage, setCapturedImage] = useState<File | null>(null);

  const captureImage = useCallback(async () => {
    try {
      setIsCapturing(true);
      
      // Request camera access
      const stream = await navigator.mediaDevices.getUserMedia({ 
        video: { facingMode: 'environment' } // Use back camera
      });
      
      // Create video element and capture frame
      const video = document.createElement('video');
      video.srcObject = stream;
      video.play();
      
      // Wait for video to be ready
      await new Promise(resolve => {
        video.onloadedmetadata = resolve;
      });
      
      // Capture frame to canvas
      const canvas = document.createElement('canvas');
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      const ctx = canvas.getContext('2d');
      ctx?.drawImage(video, 0, 0);
      
      // Convert to blob and create file
      canvas.toBlob((blob) => {
        if (blob) {
          const file = new File([blob], `receipt_${Date.now()}.jpg`, {
            type: 'image/jpeg'
          });
          setCapturedImage(file);
        }
      }, 'image/jpeg', 0.8);
      
      // Clean up
      stream.getTracks().forEach(track => track.stop());
      
    } catch (error) {
      console.error('Camera capture failed:', error);
    } finally {
      setIsCapturing(false);
    }
  }, []);

  return { captureImage, isCapturing, capturedImage, setCapturedImage };
};
```

## Offline Form Support

### Offline Data Storage
```typescript
const useOfflineFormStorage = (formId: string) => {
  const [isOnline, setIsOnline] = useState(navigator.onLine);
  const [pendingSubmissions, setPendingSubmissions] = useState<PendingSubmission[]>([]);

  const saveOfflineForm = useCallback(async (formData: any) => {
    const offlineForm: OfflineForm = {
      id: formId,
      data: formData,
      timestamp: new Date(),
      status: 'pending'
    };

    // Store in IndexedDB
    await storeOfflineForm(offlineForm);
    setPendingSubmissions(prev => [...prev, offlineForm]);
  }, [formId]);

  const syncOfflineForms = useCallback(async () => {
    if (!isOnline) return;

    const offlineForms = await getOfflineForms();
    
    for (const form of offlineForms) {
      try {
        await submitForm(form.data);
        await removeOfflineForm(form.id);
        setPendingSubmissions(prev => prev.filter(f => f.id !== form.id));
      } catch (error) {
        console.error('Failed to sync offline form:', error);
      }
    }
  }, [isOnline]);

  useEffect(() => {
    const handleOnline = () => {
      setIsOnline(true);
      syncOfflineForms();
    };

    window.addEventListener('online', handleOnline);
    return () => window.removeEventListener('online', handleOnline);
  }, [syncOfflineForms]);

  return { saveOfflineForm, pendingSubmissions, isOnline };
};
```

## Form Testing Patterns

### Validation Testing
```typescript
describe('P/O Form Validation', () => {
  test('should validate required fields in step 1', () => {
    const formData = {
      projectId: '',
      costCode: '',
      poType: ''
    };

    const validation = validateProjectSelection(formData);
    
    expect(validation.isValid).toBe(false);
    expect(validation.errors).toHaveProperty('projectId');
    expect(validation.errors).toHaveProperty('costCode');
    expect(validation.errors).toHaveProperty('poType');
  });

  test('should require ceiling amount for rolling P/Os', () => {
    const formData = {
      projectId: 'PRJ001',
      costCode: 'CC001',
      poType: 'rolling',
      ceilingAmount: undefined
    };

    const validation = validateProjectSelection(formData);
    
    expect(validation.isValid).toBe(false);
    expect(validation.errors.ceilingAmount).toContain('required');
  });
});
```

### Integration Testing
```typescript
describe('Multi-Step Form Flow', () => {
  test('should progress through all steps with valid data', async () => {
    const { getByRole, getByLabelText } = render(<POCreationForm />);
    
    // Step 1: Project Selection
    fireEvent.change(getByLabelText('IDL Project Number'), {
      target: { value: 'PRJ001' }
    });
    fireEvent.change(getByLabelText('Cost Code'), {
      target: { value: 'CC001' }
    });
    fireEvent.click(getByRole('button', { name: 'Next' }));
    
    // Step 2: Item Entry
    fireEvent.change(getByLabelText('Vendor Name'), {
      target: { value: 'Test Vendor' }
    });
    // Add line item...
    fireEvent.click(getByRole('button', { name: 'Next' }));
    
    // Step 3: Review & Submit
    expect(getByRole('button', { name: 'Submit P/O' })).toBeInTheDocument();
  });
});
```

---

*This form patterns document provides comprehensive guidelines for implementing consistent, accessible, and robust form interactions throughout the IDL Purchase Order Tool. All forms should follow these patterns for optimal user experience and code maintainability.*
