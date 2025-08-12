# IDL Purchase Order Tool - Technical Specifications

## Overview

This document provides detailed technical specifications for the IDL Purchase Order Tool, including implementation decisions, API specifications, database design, integration requirements, and business logic rules. These specifications serve as the definitive technical reference for development teams and code generation tools.

## Authentication & Security

### JWT Token Structure

**Access Token Claims:**
```json
{
  "sub": "user_uuid",
  "iat": 1640995200,
  "exp": 1640996100,
  "user_id": "12345",
  "role": "pm",
  "permissions": ["po:create", "po:submit", "po:approve"],
  "project_ids": ["PRJ001", "PRJ002", "PRJ003"]
}
```

**Token Lifecycle:**
- **Access Token Lifespan**: 15 minutes
- **Refresh Token Lifespan**: 7-30 days (configurable)
- **Storage**: Refresh tokens stored in HTTP-only cookies or encrypted database
- **Rotation**: Refresh tokens rotate on each use
- **Invalidation**: Tokens invalidated on logout or role changes

### Role-Based Permissions

**Permission Scopes:**
```typescript
enum Permission {
  // Purchase Order Operations
  PO_CREATE = 'po:create',
  PO_SUBMIT = 'po:submit',
  PO_APPROVE = 'po:approve',
  PO_CANCEL = 'po:cancel',
  PO_VIEW_ALL = 'po:view_all',
  
  // File Operations
  FILE_UPLOAD = 'file:upload',
  FILE_DELETE = 'file:delete',
  FILE_VIEW = 'file:view',
  
  // User Management
  USER_MANAGE = 'user:manage',
  USER_VIEW = 'user:view',
  
  // System Operations
  INTEGRATION_TRIGGER = 'integration:trigger',
  AUDIT_READ = 'audit:read',
  SYSTEM_CONFIG = 'system:config'
}
```

**Authorization Limits:**
```typescript
const AUTHORIZATION_LIMITS = {
  foreman: 5000.00,
  super: 25000.00,
  pm: 100000.00,
  admin: Infinity
};
```

## Database Design

### Purchase Orders Table
```sql
CREATE TABLE purchase_orders (
    id INT PRIMARY KEY AUTO_INCREMENT,
    po_number VARCHAR(20) UNIQUE NOT NULL,
    type ENUM('standard', 'rolling', 'walkup') NOT NULL,
    status ENUM('draft', 'submitted', 'approved', 'rejected', 'cancelled', 'closed') NOT NULL DEFAULT 'draft',
    
    -- Project Information
    project_id VARCHAR(50) NOT NULL,
    cost_code VARCHAR(50) NOT NULL,
    
    -- Vendor Information
    vendor_name VARCHAR(255) NOT NULL,
    vendor_contact_name VARCHAR(255),
    vendor_email VARCHAR(255),
    vendor_phone VARCHAR(50),
    
    -- Financial Information
    subtotal DECIMAL(12,2) NOT NULL DEFAULT 0.00,
    tax_amount DECIMAL(12,2) NOT NULL DEFAULT 0.00,
    total_amount DECIMAL(12,2) NOT NULL DEFAULT 0.00,
    
    -- Workflow Information
    created_by INT NOT NULL,
    submitted_by INT NULL,
    approved_by INT NULL,
    submitted_at TIMESTAMP NULL,
    approved_at TIMESTAMP NULL,
    
    -- Rolling P/O Specific
    ceiling_amount DECIMAL(12,2) NULL,
    remaining_amount DECIMAL(12,2) NULL,
    
    -- Walk-up P/O Specific
    urgency_justification TEXT NULL,
    receipt_uploaded BOOLEAN DEFAULT FALSE,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    INDEX idx_po_number (po_number),
    INDEX idx_status (status),
    INDEX idx_project_id (project_id),
    INDEX idx_deleted_at (deleted_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### P/O Line Items Table
```sql
CREATE TABLE po_line_items (
    id INT PRIMARY KEY AUTO_INCREMENT,
    po_id INT NOT NULL,
    line_number INT NOT NULL,
    
    description TEXT NOT NULL,
    quantity DECIMAL(10,3) NOT NULL,
    unit VARCHAR(50) NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    category ENUM('LA', 'EQ', 'MA', 'SC') NOT NULL,
    
    -- Tax Information
    tax_code ENUM('GST2', 'PST7', 'GP', 'GST_0', 'XMPT') NOT NULL DEFAULT 'GP',
    tax_rate DECIMAL(5,2) NOT NULL DEFAULT 12.00,
    tax_amount DECIMAL(10,2) NOT NULL DEFAULT 0.00,
    line_total DECIMAL(12,2) NOT NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (po_id) REFERENCES purchase_orders(id) ON DELETE CASCADE,
    INDEX idx_po_id (po_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### Data Retention & Soft Deletes

**Retention Policies:**
- **Audit Logs**: Minimum 2 years, partitioned by year
- **Purchase Orders**: Indefinite retention with archival after 5-7 years
- **File Uploads**: Retained with P/O, archived to cold storage after 3 years

**Soft Delete Implementation:**
- All core entities support soft deletes via `deleted_at` timestamp
- Soft-deleted records excluded from active queries using global scopes
- Admin interface allows viewing and restoring soft-deleted records

## API Specifications

### Base Configuration
- **Base URL**: `/api/v1`
- **Authentication**: Bearer JWT token in Authorization header
- **Content-Type**: `application/json`
- **Rate Limiting**: 1000 requests/hour per user

### Core Endpoints

#### Authentication
```typescript
// POST /api/v1/auth/login
interface LoginRequest {
  email: string;
  password: string;
}

interface LoginResponse {
  access_token: string;
  refresh_token: string;
  user: {
    id: number;
    email: string;
    name: string;
    role: string;
    authorization_limit: number;
    permissions: string[];
  };
  expires_in: number;
}
```

#### Purchase Orders
```typescript
// GET /api/v1/purchase-orders
interface GetPurchaseOrdersQuery {
  page?: number;
  limit?: number;
  status?: string;
  type?: string;
  project_id?: string;
}

// POST /api/v1/purchase-orders
interface CreatePurchaseOrderRequest {
  type: 'standard' | 'rolling' | 'walkup';
  project_id: string;
  cost_code: string;
  vendor_name: string;
  line_items: LineItem[];
  ceiling_amount?: number;
  urgency_justification?: string;
}
```

## Integration Requirements

### JobCost Integration
- **Base URL**: `https://api.jobcost.com/v1`
- **Authentication**: API Key in header `X-API-Key`
- **Sync Frequency**: Projects every 15-30 minutes, cost codes daily
- **Error Handling**: Retry failed requests with exponential backoff

### Timberline Integration
- **Base URL**: `https://api.timberline.com/v2`
- **Authentication**: OAuth 2.0 with client credentials
- **Sync Strategy**: Vendor data daily, P/O posting real-time on approval

### Email Integration (SendGrid)
- **Templates**: PO submitted, approved, rejected, approval required, escalation notice
- **Triggers**: Status changes, approval timeouts, escalations

## Business Logic Rules

### P/O Number Generation
**Format**: `YYJJJ-PO-XYZ`
- **YY**: 2-digit year
- **JJJ**: Julian day (001-366)
- **XYZ**: Sequential 3-digit number

**Generation Logic:**
```typescript
function generatePONumber(): string {
  const now = new Date();
  const year = now.getFullYear().toString().slice(-2);
  const julian = getJulianDay(now).toString().padStart(3, '0');
  const sequence = getNextSequenceForDay(year, julian);
  return `${year}${julian}-PO-${sequence.toString().padStart(3, '0')}`;
}
```

### Tax Calculation
**Tax Codes & Rates:**
```typescript
const TAX_RATES = {
  GST2: 0.05,   // 5% GST
  PST7: 0.07,   // 7% PST
  GP: 0.12,     // 12% Combined (GST + PST)
  GST_0: 0.00,  // GST Exempt
  XMPT: 0.00    // Fully Exempt
};
```

### Approval Logic
- **Auto-approval**: When P/O amount ≤ requester's authorization limit
- **Escalation**: Auto-escalate after 48-72 hours to next level approver
- **Delegation**: Explicit delegation with audit trails, configurable per department/project
- **Unavailable Approvers**: Pre-assigned backups, admin override with justification

### Data Validation
- **Project & Cost Codes**: Real-time validation against JobCost API
- **Vendor Validation**: Check against centralized vendor master list
- **Line Items**: Positive quantities and unit prices, valid tax codes

## Performance & Scalability

### Performance Targets
- **API Response Times**: <500ms for 95th percentile
- **Page Load Times**: <2 seconds initial load
- **Concurrent Users**: Support 100+ concurrent users
- **Database Queries**: <100ms for standard operations

### Caching Strategy
**Redis Caching:**
- **Projects**: 30 minutes TTL
- **Cost Codes**: 1 hour TTL
- **Users & Permissions**: 15 minutes TTL
- **Vendors**: 30 minutes TTL

### Database Optimization
- **Indexing**: Primary keys, foreign keys, composite indexes for common queries
- **Partitioning**: Audit logs by year, large tables by date ranges
- **Read Replicas**: For reporting queries to reduce load on primary database

## File Management

### File Upload Specifications
**Allowed Types**: PDF, DOCX, XLSX, PNG, JPG
**Limits**: 20MB per file, 50MB total per P/O, max 10 files per P/O

**Security Measures:**
- File type validation by content, not extension
- Virus scanning with ClamAV before availability
- Quarantine system for infected files
- Access control based on P/O permissions

**Storage Architecture:**
```
/uploads/{year}/{month}/{po_id}/{uuid}_{original_filename}
```

## Error Handling

### Integration Error Handling
**Retry Strategy:**
- Max 3 attempts with exponential backoff
- Base delay: 1 second, max delay: 30 seconds
- Retryable errors: timeouts, connection errors, server errors

**Fallback Mechanisms:**
- Queue failed operations for retry
- Graceful degradation for non-critical features
- User notification for persistent failures

## Environment Configuration

### Key Environment Variables
```bash
# Database
DB_HOST=localhost
DB_PORT=3306
DB_NAME=idl_po_tool
DB_USER=app_user
DB_PASSWORD=secure_password

# JWT
JWT_SECRET=jwt_secret_key
JWT_EXPIRES_IN=15m
REFRESH_TOKEN_EXPIRES_IN=30d

# Integrations
JOBCOST_API_URL=https://api.jobcost.com/v1
JOBCOST_API_KEY=jobcost_api_key
TIMBERLINE_API_URL=https://api.timberline.com/v2
SENDGRID_API_KEY=sendgrid_api_key

# File Storage
S3_BUCKET=idl-po-files
S3_REGION=us-west-2
```

### Docker Configuration
- **Backend**: Node.js 18 Alpine with production dependencies
- **Frontend**: Multi-stage build with Nginx for static serving
- **Database**: MySQL 8.0 with persistent volumes
- **Cache**: Redis 6+ with persistence enabled

---

*This technical specification document serves as the definitive reference for all implementation decisions and should be updated as requirements evolve or new technical decisions are made.*
