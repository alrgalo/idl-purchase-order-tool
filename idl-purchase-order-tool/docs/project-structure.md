   ├── services/
   │   ├── po-number.service.ts    # P/O number generation service
   │   │   ├── Standard P/Os (≤$50K): JJJJJ-PO-XXX format
   │   │   ├── High-Value P/Os (>$50K): JobNumber-PO-Sequence format
   │   │   ├── Sequence management and validation
   │   │   └── Counter-signature workflow for high-value P/Os
   │   ├── purchase-order.service.ts # P/O business logic
{{ ... }}
