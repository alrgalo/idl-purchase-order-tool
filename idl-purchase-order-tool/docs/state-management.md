// P/O number generation utility
const generatePONumber = (jobCode: string, totalAmount: number, sequence: number): string => {
  if (totalAmount > 50000) {
    // High-value P/O format: JobNumber-PO-Sequence (e.g., 4-10-061-M001)
    return `${jobCode}-PO-M${sequence.toString().padStart(3, '0')}`;
  } else {
    // Standard P/O format: JJJJJ-PO-XXX (e.g., 25301-PO-001)
    // Extract job code from full job number if needed
    const shortJobCode = jobCode.replace(/-/g, '').slice(-5);
    return `${shortJobCode}-PO-${sequence.toString().padStart(3, '0')}`;
  }
};

// Validation functions
const isValidPONumber = (poNumber: string, isHighValue: boolean): boolean => {
  if (isHighValue) {
    // High-value P/O format validation (e.g., 4-10-061-M001)
    return /^\d+-\d+-\d+-M\d{3}$/.test(poNumber);
  } else {
    // Standard P/O format validation (e.g., 25301-PO-001)
    return /^\d{5}-PO-\d{3}$/.test(poNumber);
  }
};

// Redux slice for P/O number management
const poNumberSlice = createSlice({
  name: 'poNumbers',
  initialState: {
    currentSequence: 1,
    highValueSequence: 1,
    lastGenerated: null
  },
  reducers: {
    incrementSequence: (state, action) => {
      if (action.payload.isHighValue) {
        state.highValueSequence++;
      } else {
        state.currentSequence++;
      }
    },
    setLastGenerated: (state, action) => {
      state.lastGenerated = action.payload;
    }
  }
});
