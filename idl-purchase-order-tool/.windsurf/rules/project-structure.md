---
trigger: always_on
---

# IDL Purchase Order Tool - Project Structure

## Overview

This document outlines the complete project structure for the IDL Purchase Order Tool, including both web-backend and web-frontend applications. The structure follows enterprise best practices with clear separation of concerns, scalability, and maintainability.

## Technology Stack

### Backend
- **Runtime:** Node.js 18+ LTS
- **Framework:** Express.js with TypeScript
- **Database:** MySQL 8.0+ with InnoDB
- **Caching:** Redis 6+
- **Authentication:** JWT with role-based access control
- **File Storage:** AWS S3 / Azure Blob Storage
- **Testing:** Jest + Supertest
- **Documentation:** OpenAPI/Swagger

### Frontend
- **Framework:** React 18+ with TypeScript
- **State Management:** Redux Toolkit with RTK Query
- **UI Library:** Material-UI (MUI) with custom theming
- **Build Tool:** Vite with TypeScript support
- **Routing:** React Router v6
- **Forms:** React Hook Form with Zod validation
- **Testing:** Jest + React Testing Library + Cypress
- **Mobile:** Progressive Web App (PWA) capabilities
- **Accessibility:** WCAG 2.1 AA compliant components

## Complete Project Structure

```
idl-purchase-order-tool/
├── web-backend/
│   ├── api/v1/                          # Versioned API structure
│   │   ├── controllers/                 # HTTP request handlers
│   │   ├── routes/                      # API route definitions
│   │   ├── middleware/                  # Request/response middleware
│   │   └── validators/                  # Input validation schemas
│   ├── core/
│   │   ├── models/                      # Data models and entities
│   │   ├── repositories/                # Data access layer
│   │   ├── services/                    # Business logic layer
│   │   ├── types/                       # Backend TypeScript definitions
│   │   └── utils/                       # Backend utility functions
│   ├── configs/                         # Configuration files
│   ├── database/                        # Migrations, seeds, connection
│   ├── integrations/                    # External system integrations (JobCost, Timberline, email)
│   ├── jobs/                            # Background job definitions
│   ├── websockets/                      # Real-time communication handlers
│   ├── tests/                           # Backend test suites and fixtures
│   ├── devops/                          # Docker, K8s, scripts, monitoring
│   ├── docs/                            # Backend API documentation
│   ├── app.ts                           # Express application setup
│   ├── server.ts                        # Server entry point
│   ├── package.json                     # Backend dependencies
│   ├── package-lock.json                # Backend dependency lock file
│   ├── tsconfig.json                    # Backend TypeScript config
│   ├── tsconfig.build.json              # Production TypeScript config
│   ├── jest.config.js                   # Backend testing config
│   ├── .eslintrc.js                     # Backend ESLint configuration
│   ├── .prettierrc                      # Backend Prettier configuration
│   ├── .env                             # Backend environment variables (local)
│   ├── .env.example                     # Backend environment variables template
│   ├── .env.development                 # Backend development environment
│   ├── .env.test                        # Backend test environment
│   ├── .env.production                  # Backend production environment
│   ├── .gitignore                       # Backend-specific ignore rules
│   ├── README.md                        # Backend documentation
│   ├── CHANGELOG.md                     # Backend version history
│   └── LICENSE                          # Backend license
│
├── web-frontend/
│   ├── public/                          # Static assets and PWA files
│   │   ├── icons/                       # PWA and favicon icons
│   │   ├── manifest.json                # PWA manifest
│   │   ├── robots.txt                   # SEO robots file
│   │   └── index.html                   # Main HTML template
│   ├── src/
│   │   ├── components/
│   │   │   ├── common/                  # Reusable UI components (Layout, Forms, Tables, etc.)
│   │   │   └── features/                # Feature-specific components (auth, purchase-orders, etc.)
│   │   ├── pages/                       # Page components and routing
│   │   ├── hooks/                       # Custom React hooks
│   │   ├── services/                    # API clients and utilities
│   │   ├── store/                       # Redux state management
│   │   ├── styles/                      # Themes, global styles, MUI customization
│   │   ├── types/                       # Frontend TypeScript definitions
│   │   ├── utils/                       # Frontend utility functions
│   │   ├── main.tsx                     # React application entry point
│   │   └── App.tsx                      # Main React application component
│   ├── tests/                           # Frontend test suites (Jest, RTL, Cypress)
│   ├── docs/                            # Component documentation and Storybook
│   ├── package.json                     # Frontend dependencies
│   ├── package-lock.json                # Frontend dependency lock file
│   ├── vite.config.ts                   # Vite build configuration
│   ├── tsconfig.json                    # Frontend TypeScript config
│   ├── tsconfig.node.json               # Vite TypeScript config
│   ├── jest.config.js                   # Frontend unit testing config
│   ├── cypress.config.ts                # E2E testing config
│   ├── .eslintrc.js                     # Frontend ESLint configuration
│   ├── .prettierrc                      # Frontend Prettier configuration
│   ├── .env                             # Frontend environment variables (local)
│   ├── .env.example                     # Frontend environment variables template
│   ├── .env.development                 # Frontend development environment
│   ├── .env.test                        # Frontend test environment
│   ├── .env.production                  # Frontend production environment
│   ├── .gitignore                       # Frontend-specific ignore rules
│   ├── README.md                        # Frontend documentation
│   ├── CHANGELOG.md                     # Frontend version history
│   └── LICENSE                          # Frontend license
│
├── shared/                              # Shared types, constants, and utilities
│   ├── types/                           # Shared TypeScript definitions
│   ├── constants/                       # Shared application constants
│   ├── utils/                           # Shared utility functions
│   └── schemas/                         # Shared validation schemas
│
├── docs/                                # Project-wide documentation
│   ├── user-stories.md                  # User stories and requirements
│   ├── personas.md                      # User personas
│   ├── product-backlog.md               # Product backlog and epics
│   ├── architecture.md                  # System architecture
│   ├── design-guide.md                  # Design system and UI guidelines
│   └── project-structure.md             # This document
│
├── scripts/                             # Development and deployment automation
│   ├── build.sh                         # Full-stack build script
│   ├── deploy.sh                        # Deployment automation
│   ├── dev-setup.sh                     # Development environment setup
│   └── test.sh                          # Full-stack testing script
│
├── .windsurf/                           # Windsurf IDE configuration
│   └── workflows/                       # Custom workflow definitions
│
├── client-docs/                         # Client-provided documentation
│
├── docker-compose.yml                   # Full-stack development environment
├── docker-compose.prod.yml              # Production deployment
├── README.md                            # Project overview and setup
├── LICENSE                              # Project license
├── .gitignore                           # Git ignore rules
└── CHANGELOG.md                         # Version history
│   │   ├── rate-limit.middleware.ts # Rate limiting protection
│   │   ├── cors.middleware.ts       # CORS configuration
│   │   ├── upload.middleware.ts     # File upload handling (multer)
│   │   └── audit.middleware.ts      # Audit trail logging
│   │
│   ├── models/
│   │   ├── base/
│   │   │   ├── base.model.ts        # Base model with common fields
│   │   │   ├── audit.model.ts       # Audit trail base model
│   │   │   └── index.ts             # Model exports
│   │   ├── user.model.ts            # User entity model
│   │   ├── project.model.ts         # Project entity model
│   │   ├── vendor.model.ts          # Vendor entity model
│   │   ├── cost-code.model.ts       # Cost code entity model
│   │   ├── purchase-order.model.ts  # Purchase order entity model
│   │   ├── po-line-item.model.ts    # P/O line item entity model
│   │   ├── approval.model.ts        # Approval workflow model
│   │   ├── document.model.ts        # Document metadata model
│   │   ├── notification.model.ts    # Notification model
│   │   ├── audit-log.model.ts       # Audit log model
│   │   └── index.ts                 # Model exports
│   │
│   ├── repositories/
│   │   ├── base/
│   │   │   ├── base.repository.ts   # Base repository with CRUD operations
│   │   │   ├── transaction.repository.ts # Transaction management
│   │   │   └── index.ts             # Repository exports
│   │   ├── user.repository.ts       # User data access layer
│   │   ├── project.repository.ts    # Project data access layer
│   │   ├── vendor.repository.ts     # Vendor data access layer
│   │   ├── cost-code.repository.ts  # Cost code data access layer
│   │   ├── purchase-order.repository.ts # P/O data access layer
│   │   ├── approval.repository.ts   # Approval data access layer
│   │   ├── document.repository.ts   # Document data access layer
│   │   ├── audit-log.repository.ts  # Audit log data access layer
│   │   └── index.ts                 # Repository exports
│   │
│   ├── services/
│   │   ├── auth.service.ts          # Authentication business logic
│   │   ├── user.service.ts          # User management business logic
│   │   ├── project.service.ts       # Project management business logic
│   │   ├── vendor.service.ts        # Vendor management business logic
│   │   ├── purchase-order.service.ts # P/O business logic
│   │   ├── approval.service.ts      # Approval workflow business logic
│   │   ├── document.service.ts      # Document management business logic
│   │   ├── notification.service.ts  # Notification business logic
│   │   ├── email.service.ts         # Email sending service
│   │   ├── pdf.service.ts           # PDF generation service
│   │   ├── integration.service.ts   # External system integration
│   │   ├── cache.service.ts         # Redis caching service
│   │   ├── audit.service.ts         # Audit logging service
│   │   └── index.ts                 # Service exports
│   │
│   ├── routes/
│   │   ├── v1/
│   │   │   ├── auth.routes.ts       # Authentication endpoints
│   │   │   ├── users.routes.ts      # User management endpoints
│   │   │   ├── projects.routes.ts   # Project management endpoints
│   │   │   ├── vendors.routes.ts    # Vendor management endpoints
│   │   │   ├── purchase-orders.routes.ts # P/O management endpoints
│   │   │   ├── approvals.routes.ts  # Approval workflow endpoints
│   │   │   ├── documents.routes.ts  # Document management endpoints
│   │   │   ├── reports.routes.ts    # Reporting endpoints
│   │   │   ├── notifications.routes.ts # Notification endpoints
│   │   │   ├── integrations.routes.ts # External integration endpoints
│   │   │   └── index.ts             # Route aggregator
│   │   └── index.ts                 # Main route handler
│   │
│   ├── utils/
│   │   ├── constants.ts             # Application constants
│   │   ├── enums.ts                 # Enumeration definitions
│   │   ├── helpers.ts               # Utility helper functions
│   │   ├── validators.ts            # Custom validation functions
│   │   ├── formatters.ts            # Data formatting utilities
│   │   ├── encryption.ts            # Encryption/decryption utilities
│   │   ├── logger.ts                # Logging utility (Winston)
│   │   ├── errors.ts                # Custom error classes
│   │   ├── response.ts              # Standardized API response format
│   │   └── index.ts                 # Utility exports
│   │
│   ├── types/
│   │   ├── auth.types.ts            # Authentication type definitions
│   │   ├── user.types.ts            # User type definitions
│   │   ├── purchase-order.types.ts  # P/O type definitions
│   │   ├── approval.types.ts        # Approval type definitions
│   │   ├── integration.types.ts     # External integration types
│   │   ├── api.types.ts             # API request/response types
│   │   ├── database.types.ts        # Database-related types
│   │   └── index.ts                 # Type exports
│   │
│   ├── validators/
│   │   ├── auth.validator.ts        # Authentication validation schemas
│   │   ├── user.validator.ts        # User validation schemas
│   │   ├── project.validator.ts     # Project validation schemas
│   │   ├── vendor.validator.ts      # Vendor validation schemas
│   │   ├── purchase-order.validator.ts # P/O validation schemas
│   │   ├── approval.validator.ts    # Approval validation schemas
│   │   ├── document.validator.ts    # Document validation schemas
│   │   └── index.ts                 # Validator exports
│   │
│   ├── integrations/
│   │   ├── jobcost/
│   │   │   ├── jobcost.client.ts    # JobCost API client
│   │   │   ├── jobcost.service.ts   # JobCost integration service
│   │   │   ├── jobcost.types.ts     # JobCost type definitions
│   │   │   └── index.ts             # JobCost exports
│   │   ├── timberline/
│   │   │   ├── timberline.client.ts # Timberline API client
│   │   │   ├── timberline.service.ts # Timberline integration service
│   │   │   ├── timberline.types.ts  # Timberline type definitions
│   │   │   └── index.ts             # Timberline exports
│   │   ├── email/
│   │   │   ├── sendgrid.client.ts   # SendGrid email client
│   │   │   ├── email.templates.ts   # Email template definitions
│   │   │   └── index.ts             # Email exports
│   │   └── index.ts                 # Integration exports
│   │
│   ├── database/
│   │   ├── migrations/
│   │   │   ├── 001_create_users_table.sql
│   │   │   ├── 002_create_projects_table.sql
│   │   │   ├── 003_create_vendors_table.sql
│   │   │   ├── 004_create_cost_codes_table.sql
│   │   │   ├── 005_create_purchase_orders_table.sql
│   │   │   ├── 006_create_po_line_items_table.sql
│   │   │   ├── 007_create_approvals_table.sql
│   │   │   ├── 008_create_documents_table.sql
│   │   │   ├── 009_create_audit_log_table.sql
│   │   │   └── 010_create_indexes.sql
│   │   ├── seeds/
│   │   │   ├── 001_default_users.sql
│   │   │   ├── 002_sample_projects.sql
│   │   │   ├── 003_sample_vendors.sql
│   │   │   ├── 004_cost_codes.sql
│   │   │   └── 005_system_config.sql
│   │   ├── connection.ts            # Database connection manager
│   │   ├── migration.ts             # Migration runner
│   │   └── index.ts                 # Database exports
│   │
│   ├── jobs/
│   │   ├── email-queue.job.ts       # Email sending background job
│   │   ├── notification.job.ts      # Notification processing job
│   │   ├── audit-cleanup.job.ts     # Audit log cleanup job
│   │   ├── integration-sync.job.ts  # External system sync job
│   │   ├── report-generation.job.ts # Report generation job
│   │   └── index.ts                 # Job exports
│   │
│   ├── websockets/
│   │   ├── handlers/
│   │   │   ├── po-status.handler.ts # P/O status update handler
│   │   │   ├── approval.handler.ts  # Approval notification handler
│   │   │   ├── notification.handler.ts # Real-time notification handler
│   │   │   └── index.ts             # Handler exports
│   │   ├── middleware/
│   │   │   ├── auth.middleware.ts   # WebSocket authentication
│   │   │   └── index.ts             # Middleware exports
│   │   ├── socket.ts                # WebSocket server setup
│   │   └── index.ts                 # WebSocket exports
│   │
│   └── app.ts                       # Express application setup
│   └── server.ts                    # Server entry point
│
├── tests/
│   ├── unit/
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── repositories/
│   │   ├── middleware/
│   │   └── utils/
│   ├── integration/
│   │   ├── auth.test.ts
│   │   ├── purchase-orders.test.ts
│   │   ├── approvals.test.ts
│   │   └── integrations.test.ts
│   ├── e2e/
│   │   ├── workflows/
│   │   │   ├── standard-po.test.ts
│   │   │   ├── rolling-po.test.ts
│   │   │   └── walkup-po.test.ts
│   │   └── api.test.ts
│   ├── fixtures/
│   │   ├── users.json
│   │   ├── projects.json
│   │   ├── vendors.json
│   │   └── purchase-orders.json
│   ├── helpers/
│   │   ├── database.helper.ts
│   │   ├── auth.helper.ts
│   │   └── test-server.ts
│   └── setup.ts                     # Test environment setup
│
├── docs/
│   ├── api/
│   │   ├── openapi.yaml             # OpenAPI/Swagger specification
│   │   ├── auth.md                  # Authentication documentation
│   │   ├── purchase-orders.md       # P/O API documentation
│   │   ├── approvals.md             # Approval API documentation
│   │   └── integrations.md          # Integration API documentation
│   ├── deployment/
│   │   ├── docker.md                # Docker deployment guide
│   │   ├── kubernetes.md            # Kubernetes deployment guide
│   │   ├── aws.md                   # AWS deployment guide
│   │   └── monitoring.md            # Monitoring and logging setup
│   └── development/
│       ├── setup.md                 # Development environment setup
│       ├── coding-standards.md      # Coding standards and conventions
│       ├── testing.md               # Testing guidelines
│       └── troubleshooting.md       # Common issues and solutions
│
├── scripts/
│   ├── build.sh                     # Build script
│   ├── deploy.sh                    # Deployment script
│   ├── migrate.sh                   # Database migration script
│   ├── seed.sh                      # Database seeding script
│   ├── backup.sh                    # Database backup script
│   └── health-check.sh              # Health check script
│
├── docker/
│   ├── Dockerfile                   # Production Docker image
│   ├── Dockerfile.dev               # Development Docker image
│   ├── docker-compose.yml           # Local development compose
│   ├── docker-compose.prod.yml      # Production compose
│   └── .dockerignore                # Docker ignore file
│
├── k8s/
│   ├── namespace.yaml               # Kubernetes namespace
│   ├── configmap.yaml               # Configuration map
│   ├── secret.yaml                  # Secrets configuration
│   ├── deployment.yaml              # Application deployment
│   ├── service.yaml                 # Service configuration
│   ├── ingress.yaml                 # Ingress configuration
│   └── hpa.yaml                     # Horizontal Pod Autoscaler
│
├── .env.example                     # Environment variables example
├── .env.development                 # Development environment variables
├── .env.test                        # Test environment variables
├── .gitignore                       # Git ignore file
├── .eslintrc.js                     # ESLint configuration
├── .prettierrc                      # Prettier configuration
├── jest.config.js                   # Jest testing configuration
├── tsconfig.json                    # TypeScript configuration
├── tsconfig.build.json              # Build-specific TypeScript config
├── package.json                     # NPM package configuration
├── package-lock.json                # NPM lock file
├── README.md                        # Project documentation
├── CHANGELOG.md                     # Version change log
└── LICENSE                          # Project license
```

## Key Architecture Patterns

### 1. Layered Architecture
- **Controllers:** Handle HTTP requests and responses
- **Services:** Contain business logic and orchestration
- **Repositories:** Handle data access and persistence
- **Models:** Define data structures and relationships

### 2. Dependency Injection
```typescript
// Example service with injected dependencies
export class PurchaseOrderService {
  constructor(
    private poRepository: PurchaseOrderRepository,
    private approvalService: ApprovalService,
    private notificationService: NotificationService,
    private auditService: AuditService
  ) {}
}
```

### 3. Repository Pattern
```typescript
// Base repository interface
export interface IBaseRepository<T> {
  create(entity: Partial<T>): Promise<T>;
  findById(id: number): Promise<T | null>;
  findAll(filters?: any): Promise<T[]>;
  update(id: number, updates: Partial<T>): Promise<T>;
  delete(id: number): Promise<boolean>;
}
```

### 4. Service Layer Pattern
```typescript
// Service interface example
export interface IPurchaseOrderService {
  createStandardPO(data: CreatePORequest): Promise<PurchaseOrder>;
  createRollingPO(data: CreateRollingPORequest): Promise<PurchaseOrder>;
  createWalkupPO(data: CreateWalkupPORequest): Promise<PurchaseOrder>;
  submitForApproval(poId: number, userId: number): Promise<void>;
  approvePO(poId: number, approverId: number): Promise<void>;
  rejectPO(poId: number, approverId: number, reason: string): Promise<void>;
}
```

## Database Integration

### Connection Management
```typescript
// database/connection.ts
import mysql from 'mysql2/promise';
import { config } from '../config';

export class DatabaseConnection {
  private static instance: mysql.Pool;

  public static getInstance(): mysql.Pool {
    if (!DatabaseConnection.instance) {
      DatabaseConnection.instance = mysql.createPool({
        host: config.database.host,
        port: config.database.port,
        user: config.database.user,
        password: config.database.password,
        database: config.database.name,
        waitForConnections: true,
        connectionLimit: 10,
        queueLimit: 0,
        acquireTimeout: 60000,
        timeout: 60000,
      });
    }
    return DatabaseConnection.instance;
  }
}
```

### Migration System
```typescript
// database/migration.ts
export class MigrationRunner {
  async runMigrations(): Promise<void> {
    const migrations = await this.getPendingMigrations();
    for (const migration of migrations) {
      await this.executeMigration(migration);
      await this.recordMigration(migration);
    }
  }
}
```

## Authentication & Authorization

### JWT Implementation
```typescript
// middleware/auth.middleware.ts
export const authenticateToken = (req: Request, res: Response, next: NextFunction) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];

  if (!token) {
    return res.status(401).json({ error: 'Access token required' });
  }

  jwt.verify(token, process.env.JWT_SECRET!, (err, user) => {
    if (err) return res.status(403).json({ error: 'Invalid token' });
    req.user = user;
    next();
  });
};
```

### Role-Based Access Control
```typescript
// middleware/auth.middleware.ts
export const requireRole = (roles: UserRole[]) => {
  return (req: Request, res: Response, next: NextFunction) => {
    if (!req.user || !roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Insufficient permissions' });
    }
    next();
  };
};
```

## API Documentation

### OpenAPI/Swagger Integration
```typescript
// app.ts
import swaggerUi from 'swagger-ui-express';
import { openApiSpec } from './docs/api/openapi';

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(openApiSpec));
```

### Standardized Response Format
```typescript
// utils/response.ts
export interface ApiResponse<T = any> {
  success: boolean;
  data?: T;
  error?: string;
  message?: string;
  pagination?: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}

export const successResponse = <T>(data: T, message?: string): ApiResponse<T> => ({
  success: true,
  data,
  message,
});

export const errorResponse = (error: string, message?: string): ApiResponse => ({
  success: false,
  error,
  message,
});
```

## Error Handling

### Global Error Middleware
```typescript
// middleware/error.middleware.ts
export const errorHandler = (
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  logger.error(err.stack);

  if (err instanceof ValidationError) {
    return res.status(400).json(errorResponse('Validation failed', err.message));
  }

  if (err instanceof AuthenticationError) {
    return res.status(401).json(errorResponse('Authentication failed', err.message));
  }

  if (err instanceof AuthorizationError) {
    return res.status(403).json(errorResponse('Authorization failed', err.message));
  }

  // Default server error
  res.status(500).json(errorResponse('Internal server error'));
};
```

## Testing Strategy

### Unit Tests
```typescript
// tests/unit/services/purchase-order.service.test.ts
describe('PurchaseOrderService', () => {
  let service: PurchaseOrderService;
  let mockRepository: jest.Mocked<PurchaseOrderRepository>;

  beforeEach(() => {
    mockRepository = createMockRepository();
    service = new PurchaseOrderService(mockRepository);
  });

  describe('createStandardPO', () => {
    it('should create a standard P/O successfully', async () => {
      // Test implementation
    });

    it('should enforce authorization limits', async () => {
      // Test implementation
    });
  });
});
```

### Integration Tests
```typescript
// tests/integration/purchase-orders.test.ts
describe('Purchase Orders API', () => {
  let app: Express;
  let server: Server;

  beforeAll(async () => {
    app = await createTestApp();
    server = app.listen(0);
  });

  afterAll(async () => {
    await server.close();
  });

  describe('POST /api/v1/purchase-orders', () => {
    it('should create a new P/O', async () => {
      const response = await request(app)
        .post('/api/v1/purchase-orders')
        .set('Authorization', `Bearer ${validToken}`)
        .send(validPOData)
        .expect(201);

      expect(response.body.success).toBe(true);
      expect(response.body.data.id).toBeDefined();
    });
  });
});
```

## Deployment Configuration

### Docker Configuration
```dockerfile
# Dockerfile
FROM node:18-alpine AS builder

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

FROM node:18-alpine AS production

WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./

EXPOSE 3000
CMD ["node", "dist/server.js"]
```

### Kubernetes Deployment
```yaml
# k8s/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: idl-po-backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: idl-po-backend
  template:
    metadata:
      labels:
        app: idl-po-backend
    spec:
      containers:
      - name: backend
        image: idl-po-backend:latest
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          value: "production"
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: url
```

## Development Guidelines

### Code Standards
- Use TypeScript for all source code
- Follow ESLint and Prettier configurations
- Implement comprehensive error handling
- Write unit tests for all business logic
- Document all public APIs with JSDoc
- Use meaningful variable and function names
- Follow SOLID principles

### Git Workflow
- Use feature branches for development
- Require pull request reviews
- Run automated tests on all commits
- Use conventional commit messages
- Tag releases with semantic versioning

### Performance Considerations
- Implement database query optimization
- Use Redis for caching frequently accessed data
- Implement connection pooling for database connections
- Use compression middleware for API responses
- Monitor application performance with APM tools

This backend structure provides a solid foundation for the IDL Purchase Order Tool, ensuring scalability, maintainability, and adherence to best practices for enterprise Node.js applications.
