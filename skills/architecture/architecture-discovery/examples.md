# Architecture Discovery - Examples

## Example 1: E-commerce Microservices System

### Context
Joining a team with a 3-year-old e-commerce platform. Documentation is outdated. Need to understand the architecture before planning a major feature.

### Discovery Process

#### Step 1: Initial Investigation

**Found repositories:**
- `ecommerce-web` (React frontend)
- `api-gateway` (Node.js)
- `auth-service` (Node.js)
- `product-service` (Java Spring Boot)
- `order-service` (Java Spring Boot)
- `payment-service` (Node.js)
- `notification-service` (Python)
- `infrastructure` (Terraform)

**Found documentation:**
- Outdated architecture diagram (18 months old)
- README files in each repo
- Some ADRs in `docs/` folder

#### Step 2: Component Analysis

**Web Application:**
- Technology: React, Next.js
- Purpose: Customer-facing e-commerce site
- Communicates with: API Gateway

**API Gateway:**
- Technology: Node.js, Express
- Purpose: Route requests to backend services
- Communicates with: All backend services
- Authentication: JWT validation

**Auth Service:**
- Technology: Node.js, Express
- Purpose: User authentication and authorization
- Database: PostgreSQL (users, sessions)
- APIs: Login, Register, Password Reset

**Product Service:**
- Technology: Java, Spring Boot
- Purpose: Product catalog management
- Database: PostgreSQL (products, categories)
- Cache: Redis (product data)
- APIs: Product CRUD, Search

**Order Service:**
- Technology: Java, Spring Boot
- Purpose: Order management
- Database: PostgreSQL (orders, order items)
- Communicates with: Product Service, Payment Service
- Publishes events: OrderCreated, OrderShipped

**Payment Service:**
- Technology: Node.js, Express
- Purpose: Payment processing
- Database: PostgreSQL (transactions)
- External: Stripe API
- Publishes events: PaymentSucceeded, PaymentFailed

**Notification Service:**
- Technology: Python, Flask
- Purpose: Send emails and SMS
- Consumes events: OrderCreated, PaymentSucceeded
- External: SendGrid, Twilio

#### Step 3: Communication Patterns

**Synchronous (REST):**
```
Web App → API Gateway → Auth Service (login)
Web App → API Gateway → Product Service (browse products)
Web App → API Gateway → Order Service (create order)
Order Service → Product Service (check inventory)
Order Service → Payment Service (process payment)
```

**Asynchronous (RabbitMQ):**
```
Order Service → [OrderCreated] → Notification Service
Payment Service → [PaymentSucceeded] → Notification Service
Payment Service → [PaymentFailed] → Order Service
```

#### Step 4: Data Architecture

**Databases:**
- `auth-db` (PostgreSQL): Owned by Auth Service
- `product-db` (PostgreSQL): Owned by Product Service
- `order-db` (PostgreSQL): Owned by Order Service
- `payment-db` (PostgreSQL): Owned by Payment Service
- `redis-cache`: Shared by Product Service and Order Service

**Data Flows:**
```
User Registration → Auth Service → auth-db
Browse Products → Product Service → product-db → redis-cache
Create Order → Order Service → order-db
            → Product Service (inventory check)
            → Payment Service → payment-db → Stripe
            → [OrderCreated event] → Notification Service → SendGrid
```

#### Step 5: Infrastructure

**Cloud Provider:** AWS

**Services Used:**
- ECS (Elastic Container Service) for service hosting
- RDS (PostgreSQL) for databases
- ElastiCache (Redis) for caching
- Amazon MQ (RabbitMQ) for messaging
- CloudFront for CDN
- Route 53 for DNS
- ALB (Application Load Balancer)

**Deployment:**
- Each service is a Docker container
- Deployed to ECS Fargate
- Auto-scaling based on CPU/memory
- Blue/green deployments

#### Step 6: Architecture Diagrams

**System Context:**
```
Customers → Web App → E-commerce System → Stripe (payments)
                                        → SendGrid (emails)
                                        → Twilio (SMS)
```

**Container Diagram:**
```
[Web App (React)] → [API Gateway (Node.js)]
                         ↓
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
  [Auth Service]  [Product Service]  [Order Service]
        ↓                ↓                ↓
   [auth-db]       [product-db]       [order-db]
                        ↓
                   [Redis Cache]
                   
[Order Service] → [RabbitMQ] → [Notification Service]
```

### Findings

**Strengths:**
- Clear service boundaries
- Each service owns its data
- Event-driven for async operations
- Good separation of concerns

**Issues:**
- Shared Redis cache (coupling)
- No API versioning
- No circuit breakers
- Limited observability

**Recommendations:**
1. Implement API versioning
2. Add circuit breakers (Hystrix/Resilience4j)
3. Improve monitoring (add distributed tracing)
4. Consider separate cache per service

---

## Example 2: Legacy Monolith Application

### Context
Inherited a 10-year-old monolithic application. Need to understand it before planning modernization.

### Discovery Process

#### Step 1: Initial Investigation

**Repository:**
- Single repo: `legacy-app`
- Technology: Java, Spring MVC, JSP
- 500K+ lines of code
- Minimal documentation

**Structure:**
```
legacy-app/
├── src/main/java/com/company/
│   ├── controller/
│   ├── service/
│   ├── dao/
│   ├── model/
│   └── util/
├── src/main/webapp/
│   ├── WEB-INF/
│   └── jsp/
└── src/main/resources/
```

#### Step 2: Component Analysis

**Layers:**
- **Controllers**: Handle HTTP requests (50+ controllers)
- **Services**: Business logic (100+ service classes)
- **DAOs**: Database access (80+ DAO classes)
- **Models**: Domain entities (150+ classes)

**Modules (by package):**
- `user`: User management
- `product`: Product catalog
- `order`: Order processing
- `payment`: Payment handling
- `inventory`: Inventory management
- `reporting`: Reports and analytics
- `admin`: Admin functions

#### Step 3: Database Analysis

**Single Database:**
- PostgreSQL
- 120+ tables
- Heavy use of stored procedures
- Complex foreign key relationships

**Key Tables:**
- `users`, `roles`, `permissions` (auth)
- `products`, `categories`, `prices` (catalog)
- `orders`, `order_items`, `shipments` (orders)
- `payments`, `transactions` (payments)
- `inventory`, `stock_movements` (inventory)

**Data Access Patterns:**
- Direct SQL queries (not ORM)
- Some business logic in stored procedures
- Transactions managed at service layer

#### Step 4: Integration Points

**External Systems:**
- Payment gateway (SOAP API)
- Shipping provider (REST API)
- Email service (SMTP)
- Legacy ERP system (database link)

**Integration Patterns:**
- Synchronous HTTP calls
- Direct database access to ERP (anti-pattern)
- Scheduled batch jobs for reporting

#### Step 5: Infrastructure

**Deployment:**
- Single Tomcat server
- PostgreSQL database server
- Apache HTTP Server (reverse proxy)

**Scaling:**
- Vertical scaling only
- Manual deployment
- No load balancing

### Findings

**Architecture Pattern:**
Traditional 3-tier monolith:
```
Presentation (JSP) → Business Logic (Services) → Data Access (DAO) → Database
```

**Strengths:**
- Simple deployment
- ACID transactions
- Consistent data

**Issues:**
- Tight coupling between modules
- Shared database (can't split easily)
- No horizontal scaling
- Long deployment times
- Difficult to test
- Technology stack is outdated

**Modernization Recommendations:**
1. Extract bounded contexts (user, product, order)
2. Introduce API layer
3. Migrate to microservices incrementally
4. Replace direct ERP database access with API
5. Implement CI/CD
6. Add monitoring and logging

---

## Example 3: Serverless Application

### Context
Need to understand a serverless application built on AWS to add new features.

### Discovery Process

#### Step 1: Infrastructure Analysis

**AWS Services Used:**
- Lambda functions (20+)
- API Gateway
- DynamoDB tables (5)
- S3 buckets (3)
- SQS queues (2)
- SNS topics (3)
- CloudWatch Events (cron jobs)

**IaC:**
- Serverless Framework
- `serverless.yml` configuration

#### Step 2: Function Inventory

**API Functions:**
- `createUser` (POST /users)
- `getUser` (GET /users/{id})
- `updateUser` (PUT /users/{id})
- `deleteUser` (DELETE /users/{id})
- `listProducts` (GET /products)
- `createOrder` (POST /orders)
- `getOrder` (GET /orders/{id})

**Event-Driven Functions:**
- `processOrder` (triggered by SQS)
- `sendNotification` (triggered by SNS)
- `generateReport` (scheduled, daily)
- `cleanupOldData` (scheduled, weekly)

**S3 Event Functions:**
- `processUpload` (triggered by S3 upload)
- `generateThumbnail` (image processing)

#### Step 3: Data Architecture

**DynamoDB Tables:**
- `Users` (PK: userId)
- `Products` (PK: productId)
- `Orders` (PK: orderId, GSI: userId)
- `Sessions` (PK: sessionId, TTL enabled)
- `AuditLog` (PK: timestamp#userId)

**S3 Buckets:**
- `uploads` (user uploads)
- `processed` (processed files)
- `reports` (generated reports)

#### Step 4: Event Flows

**Order Creation:**
```
API Gateway → createOrder Lambda → DynamoDB (Orders)
                                 → SQS (order-queue)
                                 
SQS → processOrder Lambda → DynamoDB (update order)
                          → SNS (order-notification)
                          
SNS → sendNotification Lambda → SES (send email)
```

**File Upload:**
```
S3 Upload → processUpload Lambda → Validate file
                                 → S3 (processed bucket)
                                 → DynamoDB (metadata)
```

### Findings

**Architecture Pattern:**
Event-driven serverless

**Strengths:**
- Auto-scaling
- Pay-per-use
- No server management
- Event-driven

**Issues:**
- Cold start latency
- Difficult to test locally
- Vendor lock-in
- Complex debugging
- No shared code between functions (duplication)

**Recommendations:**
1. Extract shared code to Lambda layers
2. Implement distributed tracing (X-Ray)
3. Add integration tests
4. Implement circuit breakers for external calls
5. Add API versioning
