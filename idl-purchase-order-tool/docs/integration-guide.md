# IDL Purchase Order Tool - Integration Guide

## Overview

This document provides comprehensive integration specifications for external systems used by the IDL Purchase Order Tool. The system integrates with JobCost for project and cost code management, Timberline for accounting and vendor data, and SendGrid for email communications.

## Integration Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   IDL P/O Tool  │    │    JobCost      │    │   Timberline    │
│                 │    │                 │    │                 │
│ ┌─────────────┐ │    │ ┌─────────────┐ │    │ ┌─────────────┐ │
│ │ Integration │◄┼────┼►│     API     │ │    │ │     API     │ │
│ │   Service   │ │    │ │             │ │    │ │             │ │
│ └─────────────┘ │    │ └─────────────┘ │    │ └─────────────┘ │
│                 │    │                 │    │                 │
│ ┌─────────────┐ │    │ ┌─────────────┐ │    │ ┌─────────────┐ │
│ │   Cache     │ │    │ │  Projects   │ │    │ │   Vendors   │ │
│ │  (Redis)    │ │    │ │ Cost Codes  │ │    │ │ Accounting  │ │
│ └─────────────┘ │    │ └─────────────┘ │    │ └─────────────┘ │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │
         │
         ▼
┌─────────────────┐
│    SendGrid     │
│                 │
│ ┌─────────────┐ │
│ │   Email     │ │
│ │ Templates   │ │
│ └─────────────┘ │
└─────────────────┘
```

## JobCost Integration

### Overview
JobCost provides project management and cost tracking capabilities. The integration synchronizes project data, cost codes, and posts P/O expenses for budget tracking.

### API Configuration

**Base URL**: `https://api.jobcost.com/v1`  
**Authentication**: API Key in `X-API-Key` header  
**Rate Limiting**: 1000 requests/hour  
**Timeout**: 30 seconds per request  

### Environment Variables
```bash
JOBCOST_API_URL=https://api.jobcost.com/v1
JOBCOST_API_KEY=your_api_key_here
JOBCOST_TIMEOUT=30000
JOBCOST_RETRY_ATTEMPTS=3
JOBCOST_RETRY_DELAY=1000
```

### API Endpoints

#### GET /projects
Retrieve all active projects.

**Request:**
```http
GET /projects?status=active&limit=100&offset=0
X-API-Key: your_api_key_here
Content-Type: application/json
```

**Response:**
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
      "end_date": "2025-12-31",
      "manager": "John Smith",
      "location": "123 Main St, Downtown",
      "last_updated": "2025-07-29T10:00:00Z"
    }
  ],
  "pagination": {
    "total": 45,
    "limit": 100,
    "offset": 0,
    "has_more": false
  }
}
```

#### GET /projects/{id}/cost-codes
Retrieve cost codes for a specific project.

**Request:**
```http
GET /projects/PRJ001/cost-codes?active=true
X-API-Key: your_api_key_here
```

**Response:**
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
      "active": true,
      "last_updated": "2025-07-29T09:30:00Z"
    }
  ]
}
```

#### POST /expenses
Post P/O expense to JobCost for budget tracking.

**Request:**
```http
POST /expenses
X-API-Key: your_api_key_here
Content-Type: application/json

{
  "project_id": "PRJ001",
  "cost_code": "CC-001",
  "amount": 1500.00,
  "description": "Lumber and materials - P/O 25210-PO-001",
  "po_number": "25210-PO-001",
  "vendor_name": "ABC Supply Co.",
  "expense_date": "2025-07-29",
  "category": "Material",
  "reference_id": "po_1"
}
```

**Response:**
```json
{
  "id": "EXP-12345",
  "status": "posted",
  "posted_at": "2025-07-29T11:00:00Z",
  "budget_impact": {
    "remaining_budget": 148500.00,
    "percent_used": 51.0
  }
}
```

### Integration Implementation

#### JobCost Service Class
```typescript
export class JobCostService {
  private readonly apiUrl: string;
  private readonly apiKey: string;
  private readonly timeout: number;
  private readonly retryAttempts: number;

  constructor() {
    this.apiUrl = process.env.JOBCOST_API_URL!;
    this.apiKey = process.env.JOBCOST_API_KEY!;
    this.timeout = parseInt(process.env.JOBCOST_TIMEOUT || '30000');
    this.retryAttempts = parseInt(process.env.JOBCOST_RETRY_ATTEMPTS || '3');
  }

  async getProjects(status: string = 'active'): Promise<JobCostProject[]> {
    const response = await this.makeRequest('GET', '/projects', {
      status,
      limit: 100
    });
    return response.data;
  }

  async getCostCodes(projectId: string): Promise<JobCostCostCode[]> {
    const response = await this.makeRequest('GET', `/projects/${projectId}/cost-codes`, {
      active: true
    });
    return response.data;
  }

  async postExpense(expense: JobCostExpenseRequest): Promise<JobCostExpenseResponse> {
    return await this.makeRequest('POST', '/expenses', expense);
  }

  private async makeRequest(method: string, endpoint: string, data?: any): Promise<any> {
    const config = {
      method,
      url: `${this.apiUrl}${endpoint}`,
      headers: {
        'X-API-Key': this.apiKey,
        'Content-Type': 'application/json'
      },
      timeout: this.timeout,
      ...(data && (method === 'GET' ? { params: data } : { data }))
    };

    return await this.retryRequest(config);
  }

  private async retryRequest(config: any, attempt: number = 1): Promise<any> {
    try {
      const response = await axios(config);
      return response.data;
    } catch (error) {
      if (attempt < this.retryAttempts && this.isRetryableError(error)) {
        await this.delay(this.retryAttempts * 1000);
        return this.retryRequest(config, attempt + 1);
      }
      throw new IntegrationError('JobCost API Error', error);
    }
  }

  private isRetryableError(error: any): boolean {
    return error.code === 'ECONNRESET' || 
           error.code === 'ETIMEDOUT' ||
           (error.response && error.response.status >= 500);
  }

  private delay(ms: number): Promise<void> {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}
```

#### Sync Strategy
```typescript
export class JobCostSyncService {
  private readonly jobCostService: JobCostService;
  private readonly projectRepository: ProjectRepository;
  private readonly costCodeRepository: CostCodeRepository;

  async syncProjects(): Promise<void> {
    try {
      const projects = await this.jobCostService.getProjects();
      
      for (const project of projects) {
        await this.projectRepository.upsert({
          id: project.id,
          name: project.name,
          status: project.status,
          budget: project.budget,
          spent: project.spent,
          remaining: project.remaining,
          start_date: project.start_date,
          end_date: project.end_date,
          last_synced_at: new Date()
        });
      }

      await this.auditService.log('jobcost_sync', 'projects', {
        synced_count: projects.length,
        sync_time: new Date()
      });
    } catch (error) {
      await this.handleSyncError('projects', error);
    }
  }

  async syncCostCodes(projectId: string): Promise<void> {
    try {
      const costCodes = await this.jobCostService.getCostCodes(projectId);
      
      for (const costCode of costCodes) {
        await this.costCodeRepository.upsert({
          id: costCode.id,
          project_id: projectId,
          code: costCode.code,
          description: costCode.description,
          category: costCode.category,
          budget: costCode.budget,
          spent: costCode.spent,
          remaining: costCode.remaining,
          active: costCode.active,
          last_synced_at: new Date()
        });
      }
    } catch (error) {
      await this.handleSyncError('cost_codes', error);
    }
  }

  private async handleSyncError(entity: string, error: any): Promise<void> {
    await this.auditService.log('jobcost_sync_error', entity, {
      error: error.message,
      timestamp: new Date()
    });
    
    // Send alert to administrators
    await this.notificationService.sendAlert('JobCost Sync Failed', {
      entity,
      error: error.message
    });
  }
}
```

## Timberline Integration

### Overview
Timberline provides accounting and vendor management capabilities. The integration synchronizes vendor data and posts approved P/Os to the accounting system.

### API Configuration

**Base URL**: `https://api.timberline.com/v2`  
**Authentication**: OAuth 2.0 Client Credentials  
**Rate Limiting**: 500 requests/hour  
**Timeout**: 45 seconds per request  

### Environment Variables
```bash
TIMBERLINE_API_URL=https://api.timberline.com/v2
TIMBERLINE_CLIENT_ID=your_client_id
TIMBERLINE_CLIENT_SECRET=your_client_secret
TIMBERLINE_SCOPE=vendors,purchase_orders,accounting
TIMBERLINE_TIMEOUT=45000
```

### OAuth 2.0 Authentication

#### POST /oauth/token
Obtain access token using client credentials.

**Request:**
```http
POST /oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&
client_id=your_client_id&
client_secret=your_client_secret&
scope=vendors purchase_orders accounting
```

**Response:**
```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "vendors purchase_orders accounting"
}
```

### API Endpoints

#### GET /vendors
Retrieve vendor information.

**Request:**
```http
GET /vendors?active=true&limit=100
Authorization: Bearer access_token
```

**Response:**
```json
{
  "data": [
    {
      "id": "VEN001",
      "name": "ABC Supply Co.",
      "contact_info": {
        "primary_contact": "Jane Smith",
        "email": "orders@abcsupply.com",
        "phone": "555-0123",
        "address": {
          "street": "123 Main St",
          "city": "Anytown",
          "state": "ST",
          "zip": "12345"
        }
      },
      "payment_terms": "Net 30",
      "tax_id": "12-3456789",
      "active": true,
      "last_updated": "2025-07-29T08:00:00Z"
    }
  ]
}
```

#### POST /purchase-orders
Submit approved P/O to accounting system.

**Request:**
```http
POST /purchase-orders
Authorization: Bearer access_token
Content-Type: application/json

{
  "po_number": "25210-PO-001",
  "vendor_id": "VEN001",
  "project_code": "PRJ001",
  "cost_code": "CC-001",
  "po_date": "2025-07-29",
  "delivery_date": "2025-08-05",
  "terms": "Net 30",
  "line_items": [
    {
      "line_number": 1,
      "description": "2x4 Lumber - 8ft",
      "quantity": 50.000,
      "unit": "pcs",
      "unit_price": 12.50,
      "tax_code": "GP",
      "total": 700.00
    }
  ],
  "subtotal": 625.00,
  "tax_amount": 75.00,
  "total_amount": 700.00,
  "notes": "Delivery to job site - contact foreman"
}
```

**Response:**
```json
{
  "id": "TL-PO-12345",
  "status": "posted",
  "posted_at": "2025-07-29T11:30:00Z",
  "accounting_reference": "AP-2025-001234"
}
```

### Integration Implementation

#### Timberline Service Class
```typescript
export class TimberlineService {
  private readonly apiUrl: string;
  private readonly clientId: string;
  private readonly clientSecret: string;
  private accessToken: string | null = null;
  private tokenExpiry: Date | null = null;

  constructor() {
    this.apiUrl = process.env.TIMBERLINE_API_URL!;
    this.clientId = process.env.TIMBERLINE_CLIENT_ID!;
    this.clientSecret = process.env.TIMBERLINE_CLIENT_SECRET!;
  }

  async getVendors(): Promise<TimberlineVendor[]> {
    await this.ensureValidToken();
    const response = await this.makeRequest('GET', '/vendors', {
      active: true,
      limit: 100
    });
    return response.data;
  }

  async submitPurchaseOrder(po: TimberlinePORequest): Promise<TimberlinePOResponse> {
    await this.ensureValidToken();
    return await this.makeRequest('POST', '/purchase-orders', po);
  }

  private async ensureValidToken(): Promise<void> {
    if (!this.accessToken || !this.tokenExpiry || new Date() >= this.tokenExpiry) {
      await this.refreshToken();
    }
  }

  private async refreshToken(): Promise<void> {
    const response = await axios.post(`${this.apiUrl}/oauth/token`, 
      new URLSearchParams({
        grant_type: 'client_credentials',
        client_id: this.clientId,
        client_secret: this.clientSecret,
        scope: 'vendors purchase_orders accounting'
      }), {
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }
    );

    this.accessToken = response.data.access_token;
    this.tokenExpiry = new Date(Date.now() + (response.data.expires_in * 1000) - 60000); // 1 minute buffer
  }

  private async makeRequest(method: string, endpoint: string, data?: any): Promise<any> {
    const config = {
      method,
      url: `${this.apiUrl}${endpoint}`,
      headers: {
        'Authorization': `Bearer ${this.accessToken}`,
        'Content-Type': 'application/json'
      },
      timeout: parseInt(process.env.TIMBERLINE_TIMEOUT || '45000'),
      ...(data && (method === 'GET' ? { params: data } : { data }))
    };

    try {
      const response = await axios(config);
      return response.data;
    } catch (error) {
      throw new IntegrationError('Timberline API Error', error);
    }
  }
}
```

## SendGrid Email Integration

### Overview
SendGrid handles all email communications including P/O notifications, approval requests, and system alerts.

### Configuration

**API Key**: Stored in environment variable  
**From Email**: `noreply@idl.com`  
**Rate Limiting**: 100 emails/hour for transactional emails  

### Environment Variables
```bash
SENDGRID_API_KEY=your_sendgrid_api_key
FROM_EMAIL=noreply@idl.com
SENDGRID_TEMPLATE_PATH=/templates/
```

### Email Templates

#### P/O Submitted Template
```html
<!DOCTYPE html>
<html>
<head>
    <title>Purchase Order Submitted - {{po_number}}</title>
</head>
<body>
    <h2>Purchase Order Submitted for Approval</h2>
    <p>Dear {{approver_name}},</p>
    
    <p>A new purchase order has been submitted and requires your approval:</p>
    
    <table>
        <tr><td><strong>P/O Number:</strong></td><td>{{po_number}}</td></tr>
        <tr><td><strong>Requester:</strong></td><td>{{requester_name}}</td></tr>
        <tr><td><strong>Project:</strong></td><td>{{project_name}}</td></tr>
        <tr><td><strong>Vendor:</strong></td><td>{{vendor_name}}</td></tr>
        <tr><td><strong>Total Amount:</strong></td><td>${{total_amount}}</td></tr>
        <tr><td><strong>Submitted:</strong></td><td>{{submitted_at}}</td></tr>
    </table>
    
    <p><a href="{{approval_url}}" style="background-color: #4CAF50; color: white; padding: 10px 20px; text-decoration: none; border-radius: 4px;">Review & Approve</a></p>
    
    <p>Please review and approve this purchase order within 48 hours to avoid automatic escalation.</p>
    
    <p>Best regards,<br>IDL Purchase Order System</p>
</body>
</html>
```

#### P/O Approved Template
```html
<!DOCTYPE html>
<html>
<head>
    <title>Purchase Order Approved - {{po_number}}</title>
</head>
<body>
    <h2>Purchase Order Approved</h2>
    <p>Dear {{requester_name}},</p>
    
    <p>Your purchase order has been approved:</p>
    
    <table>
        <tr><td><strong>P/O Number:</strong></td><td>{{po_number}}</td></tr>
        <tr><td><strong>Approved By:</strong></td><td>{{approver_name}}</td></tr>
        <tr><td><strong>Approved At:</strong></td><td>{{approved_at}}</td></tr>
        <tr><td><strong>Total Amount:</strong></td><td>${{total_amount}}</td></tr>
    </table>
    
    {{#if comments}}
    <p><strong>Approver Comments:</strong><br>{{comments}}</p>
    {{/if}}
    
    <p>You may now proceed with the purchase. The P/O has been automatically sent to the vendor and posted to the accounting system.</p>
    
    <p>Best regards,<br>IDL Purchase Order System</p>
</body>
</html>
```

### Email Service Implementation

```typescript
export class EmailService {
  private readonly sendgrid: MailService;
  private readonly fromEmail: string;

  constructor() {
    this.sendgrid = new MailService();
    this.sendgrid.setApiKey(process.env.SENDGRID_API_KEY!);
    this.fromEmail = process.env.FROM_EMAIL!;
  }

  async sendPOSubmittedNotification(po: PurchaseOrder, approver: User): Promise<void> {
    const templateData = {
      po_number: po.po_number,
      requester_name: po.created_by.name,
      approver_name: approver.name,
      project_name: po.project.name,
      vendor_name: po.vendor_name,
      total_amount: po.total_amount.toFixed(2),
      submitted_at: po.submitted_at?.toLocaleString(),
      approval_url: `${process.env.FRONTEND_URL}/approvals/${po.id}`
    };

    await this.sendTemplatedEmail(
      approver.email,
      'po-submitted',
      `Purchase Order ${po.po_number} - Approval Required`,
      templateData
    );
  }

  async sendPOApprovedNotification(po: PurchaseOrder): Promise<void> {
    const templateData = {
      po_number: po.po_number,
      requester_name: po.created_by.name,
      approver_name: po.approved_by?.name,
      approved_at: po.approved_at?.toLocaleString(),
      total_amount: po.total_amount.toFixed(2),
      comments: po.approval_comments
    };

    await this.sendTemplatedEmail(
      po.created_by.email,
      'po-approved',
      `Purchase Order ${po.po_number} - Approved`,
      templateData
    );
  }

  async sendEscalationNotification(po: PurchaseOrder, escalatedTo: User): Promise<void> {
    const templateData = {
      po_number: po.po_number,
      requester_name: po.created_by.name,
      escalated_to_name: escalatedTo.name,
      hours_pending: Math.floor((Date.now() - po.submitted_at!.getTime()) / (1000 * 60 * 60)),
      total_amount: po.total_amount.toFixed(2),
      approval_url: `${process.env.FRONTEND_URL}/approvals/${po.id}`
    };

    await this.sendTemplatedEmail(
      escalatedTo.email,
      'escalation-notice',
      `ESCALATED: Purchase Order ${po.po_number} - Approval Required`,
      templateData
    );
  }

  private async sendTemplatedEmail(
    to: string,
    templateName: string,
    subject: string,
    templateData: any
  ): Promise<void> {
    const templatePath = path.join(process.env.SENDGRID_TEMPLATE_PATH!, `${templateName}.html`);
    const template = await fs.readFile(templatePath, 'utf-8');
    const compiledTemplate = Handlebars.compile(template);
    const html = compiledTemplate(templateData);

    const msg = {
      to,
      from: this.fromEmail,
      subject,
      html
    };

    try {
      await this.sendgrid.send(msg);
      
      await this.auditService.log('email_sent', 'notification', {
        to,
        template: templateName,
        subject,
        timestamp: new Date()
      });
    } catch (error) {
      await this.auditService.log('email_failed', 'notification', {
        to,
        template: templateName,
        error: error.message,
        timestamp: new Date()
      });
      throw new IntegrationError('Email sending failed', error);
    }
  }
}
```

## Error Handling & Monitoring

### Integration Error Types
```typescript
export enum IntegrationErrorType {
  CONNECTION_ERROR = 'CONNECTION_ERROR',
  AUTHENTICATION_ERROR = 'AUTHENTICATION_ERROR',
  RATE_LIMIT_ERROR = 'RATE_LIMIT_ERROR',
  VALIDATION_ERROR = 'VALIDATION_ERROR',
  NOT_FOUND_ERROR = 'NOT_FOUND_ERROR',
  SERVER_ERROR = 'SERVER_ERROR'
}

export class IntegrationError extends Error {
  constructor(
    public readonly service: string,
    public readonly type: IntegrationErrorType,
    public readonly originalError?: any
  ) {
    super(`${service} Integration Error: ${type}`);
  }
}
```

### Monitoring & Alerting
```typescript
export class IntegrationMonitoringService {
  async trackApiCall(service: string, endpoint: string, duration: number, success: boolean): Promise<void> {
    await this.metricsService.increment(`integration.${service}.calls.total`);
    await this.metricsService.histogram(`integration.${service}.duration`, duration);
    
    if (!success) {
      await this.metricsService.increment(`integration.${service}.calls.failed`);
      await this.alertService.sendAlert(`${service} Integration Failure`, {
        endpoint,
        duration,
        timestamp: new Date()
      });
    }
  }

  async checkIntegrationHealth(): Promise<IntegrationHealthStatus> {
    const results = await Promise.allSettled([
      this.checkJobCostHealth(),
      this.checkTimberlineHealth(),
      this.checkSendGridHealth()
    ]);

    return {
      jobcost: results[0].status === 'fulfilled' ? results[0].value : { healthy: false },
      timberline: results[1].status === 'fulfilled' ? results[1].value : { healthy: false },
      sendgrid: results[2].status === 'fulfilled' ? results[2].value : { healthy: false },
      overall: results.every(r => r.status === 'fulfilled')
    };
  }
}
```

## Deployment Configuration

### Docker Compose Integration Services
```yaml
version: '3.8'
services:
  integration-worker:
    build: .
    environment:
      - NODE_ENV=production
      - JOBCOST_API_URL=${JOBCOST_API_URL}
      - JOBCOST_API_KEY=${JOBCOST_API_KEY}
      - TIMBERLINE_API_URL=${TIMBERLINE_API_URL}
      - TIMBERLINE_CLIENT_ID=${TIMBERLINE_CLIENT_ID}
      - TIMBERLINE_CLIENT_SECRET=${TIMBERLINE_CLIENT_SECRET}
      - SENDGRID_API_KEY=${SENDGRID_API_KEY}
    command: npm run start:integration-worker
    depends_on:
      - redis
      - mysql
    restart: unless-stopped

  scheduler:
    build: .
    environment:
      - NODE_ENV=production
    command: npm run start:scheduler
    depends_on:
      - redis
      - mysql
    restart: unless-stopped
```

### Kubernetes Integration Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: integration-service
spec:
  replicas: 2
  selector:
    matchLabels:
      app: integration-service
  template:
    metadata:
      labels:
        app: integration-service
    spec:
      containers:
      - name: integration-service
        image: idl-po-tool/integration:latest
        env:
        - name: JOBCOST_API_KEY
          valueFrom:
            secretKeyRef:
              name: integration-secrets
              key: jobcost-api-key
        - name: TIMBERLINE_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              name: integration-secrets
              key: timberline-client-secret
        - name: SENDGRID_API_KEY
          valueFrom:
            secretKeyRef:
              name: integration-secrets
              key: sendgrid-api-key
        resources:
          requests:
            memory: "256Mi"
            cpu: "100m"
          limits:
            memory: "512Mi"
            cpu: "500m"
```

---

*This integration guide provides comprehensive specifications for all external system integrations. Regular testing and monitoring of these integrations are essential for system reliability.*
