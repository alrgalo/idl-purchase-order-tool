# IDL Purchase Order Tool - Database Schema

## Overview

This document provides the complete database schema for the IDL Purchase Order Tool, including table definitions, relationships, indexes, and data constraints. The system uses MySQL 8.0+ with InnoDB storage engine for ACID compliance and foreign key support.

## Database Configuration

**Engine**: InnoDB  
**Character Set**: utf8mb4  
**Collation**: utf8mb4_unicode_ci  
**Time Zone**: UTC for all timestamp fields  
**Soft Deletes**: Implemented via `deleted_at` timestamp columns

## Entity Relationship Diagram

```
┌─────────────┐    ┌─────────────────┐    ┌──────────────────┐
│    users    │    │ purchase_orders │    │  po_line_items   │
│             │    │                 │    │                  │
│ id (PK)     │◄──┤ created_by (FK) │    │ id (PK)          │
│ uuid        │    │ submitted_by    │    │ po_id (FK)       │◄─┐
│ email       │    │ approved_by     │    │ line_number      │  │
│ name        │    │                 │    │ description      │  │
│ role        │    │ id (PK)         │◄───┤ quantity         │  │
│ auth_limit  │    │ po_number       │    │ unit_price       │  │
│ ...         │    │ type            │    │ category         │  │
└─────────────┘    │ status          │    │ tax_code         │  │
                   │ project_id      │    │ ...              │  │
                   │ vendor_name     │    └──────────────────┘  │
                   │ total_amount    │                          │
                   │ ...             │    ┌──────────────────┐  │
                   └─────────────────┘    │  file_uploads    │  │
                            │             │                  │  │
                            │             │ id (PK)          │  │
                            └─────────────┤ po_id (FK)       │──┘
                                          │ filename         │
                                          │ file_size        │
                                          │ mime_type        │
                                          │ ...              │
                                          └──────────────────┘
```

## Core Tables

### users
Stores user account information and authorization levels.

```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    uuid VARCHAR(36) UNIQUE NOT NULL DEFAULT (UUID()),
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL,
    role ENUM('foreman', 'super', 'pm', 'admin') NOT NULL,
    authorization_limit DECIMAL(12,2) NOT NULL,
    active BOOLEAN DEFAULT TRUE,
    password_hash VARCHAR(255) NOT NULL,
    last_login TIMESTAMP NULL,
    failed_login_attempts INT DEFAULT 0,
    locked_until TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    INDEX idx_email (email),
    INDEX idx_uuid (uuid),
    INDEX idx_role (role),
    INDEX idx_active (active),
    INDEX idx_deleted_at (deleted_at),
    
    CONSTRAINT chk_auth_limit CHECK (authorization_limit >= 0),
    CONSTRAINT chk_failed_attempts CHECK (failed_login_attempts >= 0)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

**Business Rules:**
- Email must be unique and valid format
- Authorization limits: foreman (5000), super (25000), pm (100000), admin (unlimited)
- Account lockout after 5 failed login attempts for 30 minutes
- Soft delete preserves audit trail

### projects
Cached project data from JobCost integration.

```sql
CREATE TABLE projects (
    id VARCHAR(50) PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    status ENUM('active', 'closed', 'archived') NOT NULL DEFAULT 'active',
    budget DECIMAL(15,2) NULL,
    spent DECIMAL(15,2) DEFAULT 0.00,
    remaining DECIMAL(15,2) NULL,
    start_date DATE NULL,
    end_date DATE NULL,
    last_synced_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_status (status),
    INDEX idx_name (name),
    INDEX idx_last_synced (last_synced_at),
    
    CONSTRAINT chk_budget_positive CHECK (budget IS NULL OR budget >= 0),
    CONSTRAINT chk_spent_positive CHECK (spent >= 0)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### cost_codes
Cached cost code data from JobCost integration.

```sql
CREATE TABLE cost_codes (
    id VARCHAR(50) PRIMARY KEY,
    project_id VARCHAR(50) NOT NULL,
    code VARCHAR(50) NOT NULL,
    description VARCHAR(500) NOT NULL,
    category VARCHAR(100) NULL,
    budget DECIMAL(12,2) NULL,
    spent DECIMAL(12,2) DEFAULT 0.00,
    remaining DECIMAL(12,2) NULL,
    active BOOLEAN DEFAULT TRUE,
    last_synced_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (project_id) REFERENCES projects(id) ON DELETE CASCADE,
    
    INDEX idx_project_id (project_id),
    INDEX idx_code (code),
    INDEX idx_active (active),
    INDEX idx_last_synced (last_synced_at),
    UNIQUE KEY uk_project_code (project_id, code)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### vendors
Cached vendor data from Timberline integration.

```sql
CREATE TABLE vendors (
    id VARCHAR(50) PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    contact_name VARCHAR(255) NULL,
    email VARCHAR(255) NULL,
    phone VARCHAR(50) NULL,
    address TEXT NULL,
    payment_terms VARCHAR(100) NULL,
    tax_id VARCHAR(50) NULL,
    active BOOLEAN DEFAULT TRUE,
    last_synced_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_name (name),
    INDEX idx_active (active),
    INDEX idx_last_synced (last_synced_at),
    
    CONSTRAINT chk_email_format CHECK (email IS NULL OR email REGEXP '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,}$')
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### purchase_orders
Main purchase order entity with all P/O types and workflow states.

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
    vendor_id VARCHAR(50) NULL,
    vendor_name VARCHAR(255) NOT NULL,
    vendor_contact_name VARCHAR(255) NULL,
    vendor_email VARCHAR(255) NULL,
    vendor_phone VARCHAR(50) NULL,
    vendor_address TEXT NULL,
    
    -- Financial Information
    subtotal DECIMAL(12,2) NOT NULL DEFAULT 0.00,
    tax_amount DECIMAL(12,2) NOT NULL DEFAULT 0.00,
    total_amount DECIMAL(12,2) NOT NULL DEFAULT 0.00,
    
    -- Workflow Information
    created_by INT NOT NULL,
    submitted_by INT NULL,
    submitted_at TIMESTAMP NULL,
    approved_by INT NULL,
    approved_at TIMESTAMP NULL,
    rejection_reason TEXT NULL,
    rejected_at TIMESTAMP NULL,
    cancelled_by INT NULL,
    cancelled_at TIMESTAMP NULL,
    cancellation_reason TEXT NULL,
    
    -- Rolling P/O Specific Fields
    ceiling_amount DECIMAL(12,2) NULL,
    remaining_amount DECIMAL(12,2) NULL,
    
    -- Walk-up P/O Specific Fields
    urgency_justification TEXT NULL,
    receipt_uploaded BOOLEAN DEFAULT FALSE,
    
    -- Integration Status
    jobcost_synced BOOLEAN DEFAULT FALSE,
    jobcost_synced_at TIMESTAMP NULL,
    timberline_synced BOOLEAN DEFAULT FALSE,
    timberline_synced_at TIMESTAMP NULL,
    
    -- Timestamps
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    -- Foreign Keys
    FOREIGN KEY (created_by) REFERENCES users(id),
    FOREIGN KEY (submitted_by) REFERENCES users(id),
    FOREIGN KEY (approved_by) REFERENCES users(id),
    FOREIGN KEY (cancelled_by) REFERENCES users(id),
    FOREIGN KEY (project_id) REFERENCES projects(id),
    FOREIGN KEY (vendor_id) REFERENCES vendors(id),
    
    -- Indexes
    INDEX idx_po_number (po_number),
    INDEX idx_status (status),
    INDEX idx_type (type),
    INDEX idx_project_id (project_id),
    INDEX idx_cost_code (cost_code),
    INDEX idx_vendor_id (vendor_id),
    INDEX idx_created_by (created_by),
    INDEX idx_created_at (created_at),
    INDEX idx_submitted_at (submitted_at),
    INDEX idx_approved_at (approved_at),
    INDEX idx_deleted_at (deleted_at),
    INDEX idx_sync_status (jobcost_synced, timberline_synced),
    
    -- Composite Indexes for Common Queries
    INDEX idx_status_type (status, type),
    INDEX idx_project_status (project_id, status),
    INDEX idx_created_by_status (created_by, status),
    
    -- Constraints
    CONSTRAINT chk_amounts_positive CHECK (
        subtotal >= 0 AND 
        tax_amount >= 0 AND 
        total_amount >= 0
    ),
    CONSTRAINT chk_rolling_ceiling CHECK (
        type != 'rolling' OR ceiling_amount IS NOT NULL
    ),
    CONSTRAINT chk_walkup_justification CHECK (
        type != 'walkup' OR urgency_justification IS NOT NULL
    ),
    CONSTRAINT chk_submitted_fields CHECK (
        status != 'submitted' OR (submitted_by IS NOT NULL AND submitted_at IS NOT NULL)
    ),
    CONSTRAINT chk_approved_fields CHECK (
        status != 'approved' OR (approved_by IS NOT NULL AND approved_at IS NOT NULL)
    )
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### po_line_items
Individual line items within purchase orders.

```sql
CREATE TABLE po_line_items (
    id INT PRIMARY KEY AUTO_INCREMENT,
    po_id INT NOT NULL,
    line_number INT NOT NULL,
    
    -- Item Details
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
    
    -- Additional Fields
    notes TEXT NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (po_id) REFERENCES purchase_orders(id) ON DELETE CASCADE,
    
    INDEX idx_po_id (po_id),
    INDEX idx_category (category),
    INDEX idx_tax_code (tax_code),
    UNIQUE KEY uk_po_line (po_id, line_number),
    
    CONSTRAINT chk_quantity_positive CHECK (quantity > 0),
    CONSTRAINT chk_unit_price_positive CHECK (unit_price >= 0),
    CONSTRAINT chk_tax_rate_valid CHECK (tax_rate >= 0 AND tax_rate <= 100),
    CONSTRAINT chk_line_total_positive CHECK (line_total >= 0)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### approval_workflows
Tracks approval workflow states and history.

```sql
CREATE TABLE approval_workflows (
    id INT PRIMARY KEY AUTO_INCREMENT,
    po_id INT NOT NULL,
    approver_id INT NOT NULL,
    delegated_from INT NULL,
    
    status ENUM('pending', 'approved', 'rejected', 'escalated', 'cancelled') NOT NULL DEFAULT 'pending',
    action_taken_at TIMESTAMP NULL,
    comments TEXT NULL,
    
    -- Escalation Information
    escalated_at TIMESTAMP NULL,
    escalation_reason VARCHAR(255) NULL,
    escalated_to INT NULL,
    
    -- Delegation Information
    delegation_start_date TIMESTAMP NULL,
    delegation_end_date TIMESTAMP NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (po_id) REFERENCES purchase_orders(id) ON DELETE CASCADE,
    FOREIGN KEY (approver_id) REFERENCES users(id),
    FOREIGN KEY (delegated_from) REFERENCES users(id),
    FOREIGN KEY (escalated_to) REFERENCES users(id),
    
    INDEX idx_po_id (po_id),
    INDEX idx_approver_id (approver_id),
    INDEX idx_status (status),
    INDEX idx_created_at (created_at),
    INDEX idx_escalated_at (escalated_at),
    
    CONSTRAINT chk_escalation_fields CHECK (
        status != 'escalated' OR (escalated_at IS NOT NULL AND escalation_reason IS NOT NULL)
    ),
    CONSTRAINT chk_action_timestamp CHECK (
        status = 'pending' OR action_taken_at IS NOT NULL
    )
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### file_uploads
Manages file attachments for purchase orders.

```sql
CREATE TABLE file_uploads (
    id INT PRIMARY KEY AUTO_INCREMENT,
    po_id INT NOT NULL,
    
    -- File Information
    original_filename VARCHAR(255) NOT NULL,
    stored_filename VARCHAR(255) NOT NULL,
    file_path VARCHAR(500) NOT NULL,
    file_size BIGINT NOT NULL,
    mime_type VARCHAR(100) NOT NULL,
    file_hash VARCHAR(64) NOT NULL, -- SHA-256 for deduplication
    
    -- Security Information
    virus_scan_status ENUM('pending', 'clean', 'infected', 'failed') DEFAULT 'pending',
    virus_scan_at TIMESTAMP NULL,
    virus_scan_result TEXT NULL,
    
    -- Upload Information
    uploaded_by INT NOT NULL,
    upload_ip VARCHAR(45) NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    
    FOREIGN KEY (po_id) REFERENCES purchase_orders(id) ON DELETE CASCADE,
    FOREIGN KEY (uploaded_by) REFERENCES users(id),
    
    INDEX idx_po_id (po_id),
    INDEX idx_uploaded_by (uploaded_by),
    INDEX idx_virus_scan_status (virus_scan_status),
    INDEX idx_file_hash (file_hash),
    INDEX idx_created_at (created_at),
    INDEX idx_deleted_at (deleted_at),
    
    CONSTRAINT chk_file_size_positive CHECK (file_size > 0),
    CONSTRAINT chk_virus_scan_timestamp CHECK (
        virus_scan_status = 'pending' OR virus_scan_at IS NOT NULL
    )
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### user_sessions
Tracks active user sessions for security and audit purposes.

```sql
CREATE TABLE user_sessions (
    id VARCHAR(128) PRIMARY KEY, -- Session ID
    user_id INT NOT NULL,
    refresh_token_hash VARCHAR(255) NOT NULL,
    
    -- Session Information
    ip_address VARCHAR(45) NOT NULL,
    user_agent TEXT NULL,
    device_fingerprint VARCHAR(255) NULL,
    
    -- Security Information
    is_active BOOLEAN DEFAULT TRUE,
    last_activity_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    
    INDEX idx_user_id (user_id),
    INDEX idx_refresh_token_hash (refresh_token_hash),
    INDEX idx_expires_at (expires_at),
    INDEX idx_last_activity (last_activity_at),
    INDEX idx_active (is_active)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### audit_logs
Comprehensive audit trail for all system actions.

```sql
CREATE TABLE audit_logs (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    
    -- Entity Information
    entity_type VARCHAR(50) NOT NULL,
    entity_id VARCHAR(50) NOT NULL,
    action VARCHAR(50) NOT NULL,
    
    -- User Information
    user_id INT NULL,
    user_role VARCHAR(20) NULL,
    user_email VARCHAR(255) NULL,
    
    -- Change Information
    old_values JSON NULL,
    new_values JSON NULL,
    changed_fields JSON NULL, -- Array of changed field names
    
    -- Request Information
    ip_address VARCHAR(45) NULL,
    user_agent TEXT NULL,
    request_id VARCHAR(36) NULL,
    session_id VARCHAR(128) NULL,
    
    -- Additional Context
    context JSON NULL, -- Additional contextual information
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
    
    INDEX idx_entity (entity_type, entity_id),
    INDEX idx_user_id (user_id),
    INDEX idx_action (action),
    INDEX idx_created_at (created_at),
    INDEX idx_request_id (request_id),
    INDEX idx_session_id (session_id),
    
    -- Composite indexes for common audit queries
    INDEX idx_entity_user (entity_type, entity_id, user_id),
    INDEX idx_user_action (user_id, action, created_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### system_config
System-wide configuration settings.

```sql
CREATE TABLE system_config (
    id INT PRIMARY KEY AUTO_INCREMENT,
    config_key VARCHAR(100) UNIQUE NOT NULL,
    config_value TEXT NOT NULL,
    data_type ENUM('string', 'number', 'boolean', 'json') NOT NULL DEFAULT 'string',
    description TEXT NULL,
    is_sensitive BOOLEAN DEFAULT FALSE,
    
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    INDEX idx_config_key (config_key),
    INDEX idx_sensitive (is_sensitive)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## Views

### active_purchase_orders
View for non-deleted purchase orders with user information.

```sql
CREATE VIEW active_purchase_orders AS
SELECT 
    po.*,
    creator.name AS created_by_name,
    creator.role AS created_by_role,
    submitter.name AS submitted_by_name,
    approver.name AS approved_by_name,
    approver.role AS approved_by_role,
    p.name AS project_name,
    cc.description AS cost_code_description
FROM purchase_orders po
LEFT JOIN users creator ON po.created_by = creator.id
LEFT JOIN users submitter ON po.submitted_by = submitter.id  
LEFT JOIN users approver ON po.approved_by = approver.id
LEFT JOIN projects p ON po.project_id = p.id
LEFT JOIN cost_codes cc ON po.cost_code = cc.id
WHERE po.deleted_at IS NULL;
```

### pending_approvals
View for purchase orders requiring approval.

```sql
CREATE VIEW pending_approvals AS
SELECT 
    po.id,
    po.po_number,
    po.type,
    po.total_amount,
    po.project_id,
    po.vendor_name,
    po.submitted_at,
    aw.approver_id,
    aw.delegated_from,
    u.name AS approver_name,
    u.role AS approver_role,
    creator.name AS created_by_name,
    TIMESTAMPDIFF(HOUR, po.submitted_at, NOW()) AS hours_pending
FROM purchase_orders po
JOIN approval_workflows aw ON po.id = aw.po_id
JOIN users u ON aw.approver_id = u.id
JOIN users creator ON po.created_by = creator.id
WHERE po.status = 'submitted' 
  AND aw.status = 'pending'
  AND po.deleted_at IS NULL;
```

## Triggers

### po_number_generation
Automatically generate P/O numbers on insert.

```sql
DELIMITER //
CREATE TRIGGER tr_po_number_generation 
BEFORE INSERT ON purchase_orders
FOR EACH ROW
BEGIN
    IF NEW.po_number IS NULL OR NEW.po_number = '' THEN
        SET NEW.po_number = fn_generate_po_number();
    END IF;
END//
DELIMITER ;
```

### audit_log_trigger
Automatically log changes to purchase orders.

```sql
DELIMITER //

CREATE TRIGGER tr_purchase_orders_audit_insert
AFTER INSERT ON purchase_orders
FOR EACH ROW
BEGIN
    INSERT INTO audit_logs (
        entity_type, entity_id, action, user_id, new_values, created_at
    ) VALUES (
        'purchase_order', 
        NEW.po_id, 
        'created', 
        NEW.created_by,
        JSON_OBJECT(
            'po_number', NEW.po_number,
            'type', NEW.type,
            'status', NEW.status,
            'vendor_id', NEW.vendor_id,
            'project_id', NEW.project_id
        ),
        NOW()
    );
END//

CREATE TRIGGER tr_purchase_orders_audit_update
AFTER UPDATE ON purchase_orders
FOR EACH ROW
BEGIN
    INSERT INTO audit_logs (
        entity_type, entity_id, action, user_id, old_values, new_values, created_at
    ) VALUES (
        'purchase_order',
        NEW.po_id,
        'updated',
        NEW.created_by, -- Use created_by since there's no updated_by column
        JSON_OBJECT(
            'status', OLD.status,
            'vendor_id', OLD.vendor_id,
            'project_id', OLD.project_id
        ),
        JSON_OBJECT(
            'status', NEW.status,
            'vendor_id', NEW.vendor_id,
            'project_id', NEW.project_id
        ),
        NOW()
    );
END//

DELIMITER ;
```

## Database Constraints

### P/O Number Format Validation
Ensure P/O numbers follow the YYJJJ-PO-XYZ format.

```sql
-- Add constraint to purchase_orders table for P/O number format validation
ALTER TABLE purchase_orders 
ADD CONSTRAINT chk_po_number_format 
CHECK (po_number REGEXP '^[0-9]{2}[0-9]{3}-PO-[0-9]{3}$');

-- Add index for efficient P/O number lookups during generation
CREATE INDEX idx_po_number_prefix ON purchase_orders (po_number);
```

### P/O Number Sequence Helper View
Optional view to help with P/O number generation queries.

```sql
CREATE VIEW v_po_number_sequences AS
SELECT 
    SUBSTRING(po_number, 1, 8) as date_prefix,
    MAX(CAST(RIGHT(po_number, 3) AS UNSIGNED)) as max_sequence,
    COUNT(*) as count_for_day
FROM purchase_orders 
WHERE po_number REGEXP '^[0-9]{2}[0-9]{3}-PO-[0-9]{3}$'
GROUP BY SUBSTRING(po_number, 1, 8);
```

## Data Migration Scripts

### Initial Data Setup

```sql
-- Insert default system configuration
INSERT INTO system_config (config_key, config_value, data_type, description) VALUES
('po_approval_timeout_hours', '48', 'number', 'Hours before P/O approval auto-escalates'),
('max_file_size_mb', '20', 'number', 'Maximum file upload size in MB'),
('max_files_per_po', '10', 'number', 'Maximum number of files per P/O'),
('virus_scan_enabled', 'true', 'boolean', 'Enable virus scanning for uploads'),
('email_notifications_enabled', 'true', 'boolean', 'Enable email notifications');

-- Insert default admin user (password should be changed immediately)
INSERT INTO users (email, name, role, authorization_limit, password_hash) VALUES
('admin@idl.com', 'System Administrator', 'admin', 999999999.99, '$2b$12$...');

-- Create indexes for performance optimization
CREATE INDEX idx_po_search ON purchase_orders (po_number, vendor_name, project_id);
CREATE INDEX idx_audit_performance ON audit_logs (entity_type, created_at, user_id);
```

## Backup and Maintenance

### Backup Strategy
```sql
-- Daily backup script
mysqldump --single-transaction --routines --triggers \
  --add-drop-database --databases idl_po_tool > backup_$(date +%Y%m%d).sql

-- Weekly full backup with compression
mysqldump --single-transaction --routines --triggers \
  --add-drop-database --databases idl_po_tool | gzip > weekly_backup_$(date +%Y%m%d).sql.gz
```

### Maintenance Tasks
```sql
-- Clean up old audit logs (run monthly)
DELETE FROM audit_logs 
WHERE created_at < DATE_SUB(NOW(), INTERVAL 2 YEAR);

-- Clean up expired sessions (run daily)
DELETE FROM user_sessions 
WHERE expires_at < NOW() OR last_activity_at < DATE_SUB(NOW(), INTERVAL 30 DAY);

-- Optimize tables (run weekly)
OPTIMIZE TABLE purchase_orders, po_line_items, audit_logs;

-- Update table statistics (run weekly)
ANALYZE TABLE purchase_orders, po_line_items, users, audit_logs;
```

## Performance Considerations

### Query Optimization
- Use composite indexes for multi-column WHERE clauses
- Partition large tables (audit_logs) by date ranges
- Use covering indexes for frequently accessed columns
- Implement read replicas for reporting queries

### Storage Optimization
- Use appropriate data types (DECIMAL for currency, ENUM for status)
- Normalize vendor information to reduce duplication
- Archive old audit logs to separate tables/databases
- Compress file storage using database compression features

### Monitoring Queries
```sql
-- Monitor slow queries
SELECT * FROM mysql.slow_log WHERE start_time > DATE_SUB(NOW(), INTERVAL 1 DAY);

-- Check index usage
SELECT * FROM sys.schema_unused_indexes WHERE object_schema = 'idl_po_tool';

-- Monitor table sizes
SELECT 
    table_name,
    ROUND(((data_length + index_length) / 1024 / 1024), 2) AS 'Size (MB)'
FROM information_schema.tables 
WHERE table_schema = 'idl_po_tool'
ORDER BY (data_length + index_length) DESC;
```

---

*This database schema is designed for scalability, performance, and data integrity. Regular maintenance and monitoring are essential for optimal performance.*
