# IDL Purchase Order Tool - API Reference

## Overview

This document provides comprehensive API documentation for the IDL Purchase Order Tool backend services. All endpoints use RESTful conventions with JSON request/response bodies and JWT-based authentication.

**Base URL**: `/api/v1`  
**Authentication**: Bearer token in `Authorization` header  
**Content-Type**: `application/json`  
**Rate Limiting**: 1000 requests/hour per user, 100 requests/minute per endpoint

## Authentication Endpoints

### POST /auth/login
Authenticate user and receive access/refresh tokens.

**Request:**
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response (200):**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 123,
    "uuid": "550e8400-e29b-41d4-a716-446655440000",
    "email": "user@example.com",
    "name": "John Doe",
    "role": "pm",
    "authorization_limit": 100000.00,
    "permissions": ["po:create", "po:submit", "po:approve"],
    "project_ids": ["PRJ001", "PRJ002"]
  },
  "expires_in": 900
}
```

**Error Responses:**
- `401 Unauthorized`: Invalid credentials
- `429 Too Many Requests`: Rate limit exceeded

### POST /auth/refresh
Refresh access token using refresh token.

**Request:**
```json
{
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Response (200):**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 900
}
```

### POST /auth/logout
Invalidate current tokens.

**Headers:** `Authorization: Bearer <access_token>`

**Response (200):**
```json
{
  "message": "Successfully logged out"
}
```

## Purchase Order Endpoints

### GET /purchase-orders
Retrieve paginated list of purchase orders with filtering.

**Headers:** `Authorization: Bearer <access_token>`

**Query Parameters:**
- `page` (number, default: 1): Page number
- `limit` (number, default: 20, max: 100): Items per page
- `status` (string): Filter by status (draft, submitted, approved, rejected, cancelled, closed)
- `type` (string): Filter by type (standard, rolling, walkup)
- `project_id` (string): Filter by project ID
- `created_by` (number): Filter by creator user ID
- `date_from` (string, ISO date): Filter by creation date from
- `date_to` (string, ISO date): Filter by creation date to
- `search` (string): Search in P/O number, vendor name, or description

**Response (200):**
```json
{
  "data": [
    {
      "id": 1,
      "po_number": "25210-PO-001",
      "type": "standard",
      "status": "submitted",
      "project_id": "PRJ001",
      "cost_code": "CC-001",
      "vendor_name": "ABC Supply Co.",
      "vendor_contact_name": "Jane Smith",
      "vendor_email": "jane@abcsupply.com",
      "vendor_phone": "555-0123",
      "subtotal": 1500.00,
      "tax_amount": 180.00,
      "total_amount": 1680.00,
      "line_items_count": 3,
      "created_by": {
        "id": 123,
        "name": "John Doe",
        "role": "foreman"
      },
      "submitted_by": {
        "id": 123,
        "name": "John Doe"
      },
      "submitted_at": "2025-07-29T10:30:00Z",
      "created_at": "2025-07-29T09:15:00Z",
      "updated_at": "2025-07-29T10:30:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 45,
    "pages": 3,
    "has_next": true,
    "has_prev": false
  }
}
```

### GET /purchase-orders/{id}
Retrieve detailed purchase order information.

**Headers:** `Authorization: Bearer <access_token>`

**Response (200):**
```json
{
  "id": 1,
  "po_number": "25210-PO-001",
  "type": "standard",
  "status": "submitted",
  "project_id": "PRJ001",
  "cost_code": "CC-001",
  "vendor_name": "ABC Supply Co.",
  "vendor_contact_name": "Jane Smith",
  "vendor_email": "jane@abcsupply.com",
  "vendor_phone": "555-0123",
  "vendor_address": "123 Main St, City, State 12345",
  "subtotal": 1500.00,
  "tax_amount": 180.00,
  "total_amount": 1680.00,
  "line_items": [
    {
      "id": 1,
      "line_number": 1,
      "description": "2x4 Lumber - 8ft",
      "quantity": 50.000,
      "unit": "pcs",
      "unit_price": 12.50,
      "category": "MA",
      "tax_code": "GP",
      "tax_rate": 12.00,
      "tax_amount": 75.00,
      "line_total": 700.00
    }
  ],
  "files": [
    {
      "id": 1,
      "original_filename": "quote.pdf",
      "file_size": 245760,
      "mime_type": "application/pdf",
      "uploaded_by": "John Doe",
      "uploaded_at": "2025-07-29T09:20:00Z"
    }
  ],
  "approval_history": [
    {
      "id": 1,
      "approver": {
        "id": 456,
        "name": "Jane Manager",
        "role": "super"
      },
      "status": "pending",
      "created_at": "2025-07-29T10:30:00Z"
    }
  ],
  "created_by": {
    "id": 123,
    "name": "John Doe",
    "role": "foreman"
  },
  "created_at": "2025-07-29T09:15:00Z",
  "updated_at": "2025-07-29T10:30:00Z"
}
```

**Error Responses:**
- `404 Not Found`: P/O not found or access denied

### POST /purchase-orders
Create a new purchase order.

**Headers:** `Authorization: Bearer <access_token>`

**Request:**
```json
{
  "type": "standard",
  "project_id": "PRJ001",
  "cost_code": "CC-001",
  "vendor_name": "ABC Supply Co.",
  "vendor_contact_name": "Jane Smith",
  "vendor_email": "jane@abcsupply.com",
  "vendor_phone": "555-0123",
  "vendor_address": "123 Main St, City, State 12345",
  "line_items": [
    {
      "description": "2x4 Lumber - 8ft",
      "quantity": 50.000,
      "unit": "pcs",
      "unit_price": 12.50,
      "category": "MA",
      "tax_code": "GP"
    }
  ],
  "ceiling_amount": 5000.00,
  "urgency_justification": "Emergency repair needed"
}
```

**Response (201):**
```json
{
  "id": 1,
  "po_number": "25210-PO-001",
  "status": "draft",
  "total_amount": 700.00,
  "created_at": "2025-07-29T09:15:00Z"
}
```

**Error Responses:**
- `400 Bad Request`: Validation errors
- `403 Forbidden`: Insufficient permissions
- `422 Unprocessable Entity`: Business rule violations

### PUT /purchase-orders/{id}
Update existing purchase order (draft status only).

**Headers:** `Authorization: Bearer <access_token>`

**Request:** Same as POST /purchase-orders

**Response (200):** Same as GET /purchase-orders/{id}

**Error Responses:**
- `400 Bad Request`: Validation errors
- `403 Forbidden`: Cannot edit non-draft P/O or insufficient permissions
- `404 Not Found`: P/O not found

### PATCH /purchase-orders/{id}/submit
Submit purchase order for approval.

**Headers:** `Authorization: Bearer <access_token>`

**Response (200):**
```json
{
  "id": 1,
  "po_number": "25210-PO-001",
  "status": "submitted",
  "submitted_at": "2025-07-29T10:30:00Z",
  "approver": {
    "id": 456,
    "name": "Jane Manager",
    "role": "super"
  }
}
```

### PATCH /purchase-orders/{id}/approve
Approve purchase order (approvers only).

**Headers:** `Authorization: Bearer <access_token>`

**Request:**
```json
{
  "comments": "Approved for project requirements"
}
```

**Response (200):**
```json
{
  "id": 1,
  "po_number": "25210-PO-001",
  "status": "approved",
  "approved_at": "2025-07-29T11:00:00Z",
  "approved_by": {
    "id": 456,
    "name": "Jane Manager"
  }
}
```

### PATCH /purchase-orders/{id}/reject
Reject purchase order with reason.

**Headers:** `Authorization: Bearer <access_token>`

**Request:**
```json
{
  "reason": "Insufficient budget allocation for this project"
}
```

**Response (200):**
```json
{
  "id": 1,
  "po_number": "25210-PO-001",
  "status": "rejected",
  "rejection_reason": "Insufficient budget allocation for this project",
  "rejected_at": "2025-07-29T11:00:00Z"
}
```

### DELETE /purchase-orders/{id}
Soft delete purchase order (draft status only).

**Headers:** `Authorization: Bearer <access_token>`

**Response (204):** No content

## File Management Endpoints

### POST /purchase-orders/{id}/files
Upload file attachment to purchase order.

**Headers:** 
- `Authorization: Bearer <access_token>`
- `Content-Type: multipart/form-data`

**Request:**
```
Content-Disposition: form-data; name="file"; filename="quote.pdf"
Content-Type: application/pdf

[Binary file data]
```

**Response (201):**
```json
{
  "id": 1,
  "original_filename": "quote.pdf",
  "stored_filename": "550e8400-e29b-41d4-a716-446655440000_quote.pdf",
  "file_size": 245760,
  "mime_type": "application/pdf",
  "virus_scan_status": "pending",
  "uploaded_by": {
    "id": 123,
    "name": "John Doe"
  },
  "uploaded_at": "2025-07-29T09:20:00Z"
}
```

**Error Responses:**
- `400 Bad Request`: Invalid file type or size
- `413 Payload Too Large`: File exceeds size limit
- `422 Unprocessable Entity`: Virus detected or scan failed

### GET /purchase-orders/{id}/files
List files attached to purchase order.

**Headers:** `Authorization: Bearer <access_token>`

**Response (200):**
```json
{
  "data": [
    {
      "id": 1,
      "original_filename": "quote.pdf",
      "file_size": 245760,
      "mime_type": "application/pdf",
      "virus_scan_status": "clean",
      "uploaded_by": {
        "id": 123,
        "name": "John Doe"
      },
      "uploaded_at": "2025-07-29T09:20:00Z"
    }
  ]
}
```

### GET /files/{id}/download
Download file attachment.

**Headers:** `Authorization: Bearer <access_token>`

**Response (200):**
- `Content-Type`: Original file MIME type
- `Content-Disposition`: `attachment; filename="quote.pdf"`
- Body: Binary file data

**Error Responses:**
- `403 Forbidden`: No access to P/O or file
- `404 Not Found`: File not found
- `423 Locked`: File quarantined due to virus

### DELETE /files/{id}
Delete file attachment.

**Headers:** `Authorization: Bearer <access_token>`

**Response (204):** No content

## User Management Endpoints

### GET /users
List users with filtering (admin/PM only).

**Headers:** `Authorization: Bearer <access_token>`

**Query Parameters:**
- `page`, `limit`: Pagination
- `role`: Filter by role
- `active`: Filter by active status
- `search`: Search by name or email

**Response (200):**
```json
{
  "data": [
    {
      "id": 123,
      "uuid": "550e8400-e29b-41d4-a716-446655440000",
      "email": "user@example.com",
      "name": "John Doe",
      "role": "foreman",
      "authorization_limit": 5000.00,
      "active": true,
      "last_login": "2025-07-29T08:00:00Z",
      "created_at": "2025-01-15T10:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 25,
    "pages": 2
  }
}
```

### GET /users/me
Get current user profile.

**Headers:** `Authorization: Bearer <access_token>`

**Response (200):**
```json
{
  "id": 123,
  "uuid": "550e8400-e29b-41d4-a716-446655440000",
  "email": "user@example.com",
  "name": "John Doe",
  "role": "foreman",
  "authorization_limit": 5000.00,
  "permissions": ["po:create", "po:submit"],
  "project_ids": ["PRJ001", "PRJ002"],
  "active": true,
  "last_login": "2025-07-29T08:00:00Z",
  "created_at": "2025-01-15T10:00:00Z"
}
```

## Project & Cost Code Endpoints

### GET /projects
List available projects from JobCost integration.

**Headers:** `Authorization: Bearer <access_token>`

**Query Parameters:**
- `active`: Filter by active status (default: true)
- `search`: Search by project name or ID

**Response (200):**
```json
{
  "data": [
    {
      "id": "PRJ001",
      "name": "Downtown Office Building",
      "status": "active",
      "budget": 2500000.00,
      "spent": 1200000.00,
      "remaining": 1300000.00,
      "start_date": "2025-01-01",
      "end_date": "2025-12-31"
    }
  ]
}
```

### GET /projects/{id}/cost-codes
List cost codes for specific project.

**Headers:** `Authorization: Bearer <access_token>`

**Response (200):**
```json
{
  "data": [
    {
      "id": "CC-001",
      "code": "01-100",
      "description": "Site Preparation",
      "category": "Construction",
      "budget": 150000.00,
      "spent": 75000.00,
      "remaining": 75000.00,
      "active": true
    }
  ]
}
```

### GET /vendors
List vendors from Timberline integration.

**Headers:** `Authorization: Bearer <access_token>`

**Query Parameters:**
- `active`: Filter by active status
- `search`: Search by vendor name

**Response (200):**
```json
{
  "data": [
    {
      "id": "VEN001",
      "name": "ABC Supply Co.",
      "contact_info": {
        "email": "orders@abcsupply.com",
        "phone": "555-0123",
        "address": "123 Main St, City, State 12345"
      },
      "payment_terms": "Net 30",
      "active": true
    }
  ]
}
```

## Audit & Reporting Endpoints

### GET /audit-logs
Retrieve audit trail (admin only).

**Headers:** `Authorization: Bearer <access_token>`

**Query Parameters:**
- `page`, `limit`: Pagination
- `entity_type`: Filter by entity type (purchase_order, user, etc.)
- `entity_id`: Filter by specific entity ID
- `user_id`: Filter by user who performed action
- `action`: Filter by action type
- `date_from`, `date_to`: Date range filter

**Response (200):**
```json
{
  "data": [
    {
      "id": 1001,
      "entity_type": "purchase_order",
      "entity_id": "1",
      "action": "created",
      "user": {
        "id": 123,
        "name": "John Doe",
        "role": "foreman"
      },
      "old_values": null,
      "new_values": {
        "po_number": "25210-PO-001",
        "status": "draft",
        "total_amount": 700.00
      },
      "ip_address": "192.168.1.100",
      "created_at": "2025-07-29T09:15:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 50,
    "total": 1250,
    "pages": 25
  }
}
```

## Error Response Format

All API endpoints return errors in a consistent format:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The request contains invalid data",
    "details": {
      "field": "email",
      "message": "Email format is invalid"
    },
    "timestamp": "2025-07-29T10:30:00Z",
    "request_id": "req_550e8400-e29b-41d4-a716-446655440000"
  }
}
```

**Common Error Codes:**
- `VALIDATION_ERROR`: Request validation failed
- `UNAUTHORIZED`: Authentication required or failed
- `FORBIDDEN`: Insufficient permissions
- `NOT_FOUND`: Resource not found
- `CONFLICT`: Resource conflict (duplicate, constraint violation)
- `RATE_LIMITED`: Too many requests
- `INTERNAL_ERROR`: Server error
- `INTEGRATION_ERROR`: External service error

## Rate Limiting

**Global Limits:**
- 1000 requests per hour per user
- 100 requests per minute per endpoint
- 10 file uploads per minute per user

**Headers in Response:**
- `X-RateLimit-Limit`: Request limit
- `X-RateLimit-Remaining`: Remaining requests
- `X-RateLimit-Reset`: Reset timestamp

**Rate Limit Exceeded Response (429):**
```json
{
  "error": {
    "code": "RATE_LIMITED",
    "message": "Too many requests. Please try again later.",
    "retry_after": 60,
    "timestamp": "2025-07-29T10:30:00Z",
    "request_id": "req_550e8400-e29b-41d4-a716-446655440000"
  }
}
```

---

*This API reference is automatically generated from OpenAPI specifications and should be kept in sync with the actual implementation.*
