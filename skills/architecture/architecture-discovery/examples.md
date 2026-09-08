# Architecture Discovery - Practical Examples

## Example 1: Discovering a Microservices Architecture

### Scenario
You've joined a team with a microservices-based SaaS platform. Documentation is minimal.

### Discovery Process

#### Step 1: Initial Exploration (1 hour)

**Repository structure:**
```
platform/
├── services/
│   ├── api-gateway/
│   ├── auth-service/
│   ├── user-service/
│   ├── product-service/
│   ├── order-service/
│   ├── payment-service/
│   ├── notification-service/
│   └── analytics-service/
├── shared/
│   ├── common-lib/
│   └── proto-definitions/
├── infrastructure/
│   ├── kubernetes/
│   └── terraform/
└── docker-compose.yml
```

**Initial findings:**
- 8 microservices
- Shared library for common code
- Proto definitions suggest gRPC
- Kubernetes for deployment
- Terraform for infrastructure

#### Step 2: Technology Stack Analysis (30 minutes)

**Per-service analysis:**

`api-gateway/package.json`:
```json
{
  "dependencies": {
    "express": "^4.17.1",
    "http-proxy-middleware": "^2.0.0",
    "express-rate-limit": "^5.2.6"
  }
}
```
→ Node.js/Express gateway with rate limiting

`auth-service/pom.xml`:
```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
  </dependency>
  <dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
  </dependency>
</dependencies>
```
→ Java/Spring Boot with JWT authentication

**Technology inventory:**
```markdown
| Service | Language | Framework | Database |
|---------|----------|-----------|----------|
| api-gateway | Node.js | Express | - |
| auth-service | Java | Spring Boot | PostgreSQL |
| user-service | Java | Spring Boot | PostgreSQL |
| product-service | Python | FastAPI | MongoDB |
| order-service | Java | Spring Boot | PostgreSQL |
| payment-service | Node.js | Express | PostgreSQL |
| notification-service | Python | Flask | Redis |
| analytics-service | Python | FastAPI | ClickHouse |
```

#### Step 3: Communication Patterns (2 hours)

**Found in api-gateway/routes.js:**
```javascript
app.use('/api/auth', proxy('http://auth-service:8080'));
app.use('/api/users', proxy('http://user-service:8080'));
app.use('/api/products', proxy('http://product-service:8000'));
```
→ Synchronous REST via API gateway

**Found in order-service/config:**
```yaml
rabbitmq:
  host: rabbitmq
  exchanges:
    - name: orders
      type: topic
  queues:
    - name: order.created
    - name: order.completed
```
→ Asynchronous messaging via RabbitMQ

**Communication diagram:**
```
Client
  ↓
API Gateway (REST)
  ↓
Services (REST for sync, RabbitMQ for async)
  ↓
Databases
```

#### Step 4: Data Flow Analysis (2 hours)

**Order creation flow:**

1. Client → API Gateway → Order Service (REST)
2. Order Service validates and saves to PostgreSQL
3. Order Service publishes `order.created` event to RabbitMQ
4. Payment Service consumes event, processes payment
5. Payment Service publishes `payment.processed` event
6. Order Service consumes event, updates order status
7. Notification Service consumes event, sends email
8. Analytics Service consumes event, updates metrics

**Data flow diagram:**
```
[Client]
   ↓ POST /orders
[API Gateway]
   ↓
[Order Service] --save--> [PostgreSQL]
   ↓ publish: order.created
[RabbitMQ]
   ↓
[Payment Service] --process--> [Stripe API]
   ↓ publish: payment.processed
[RabbitMQ]
   ├─→ [Order Service] --update--> [PostgreSQL]
   ├─→ [Notification Service] --send--> [Email Service]
   └─→ [Analytics Service] --insert--> [ClickHouse]
```

#### Step 5: Deployment Architecture (1 hour)

**From kubernetes/ manifests:**

```yaml
# Each service has:
- Deployment (2-5 replicas)
- Service (ClusterIP)
- HorizontalPodAutoscaler
- ConfigMap
- Secret

# Infrastructure:
- Ingress (Nginx)
- PostgreSQL (StatefulSet)
- MongoDB (StatefulSet)
- RabbitMQ (StatefulSet)
- Redis (StatefulSet)
```

**Deployment diagram:**
```
[Internet]
   ↓
[AWS ALB]
   ↓
[Nginx Ingress]
   ↓
[Kubernetes Cluster]
   ├─ [API Gateway Pods] (3 replicas)
   ├─ [Auth Service Pods] (2 replicas)
   ├─ [User Service Pods] (2 replicas)
   ├─ [Product Service Pods] (3 replicas)
   ├─ [Order Service Pods] (5 replicas)
   ├─ [Payment Service Pods] (3 replicas)
   ├─ [Notification Service Pods] (2 replicas)
   └─ [Analytics Service Pods] (2 replicas)

[Data Layer]
   ├─ [PostgreSQL Cluster]
   ├─ [MongoDB Cluster]
   ├─ [RabbitMQ Cluster]
   └─ [Redis Cluster]
```

#### Step 6: Findings Summary

**Architecture Overview:**
- Microservices architecture with 8 services
- Polyglot (Java, Node.js, Python)
- Synchronous communication via REST
- Asynchronous communication via RabbitMQ
- Deployed on Kubernetes (AWS EKS)
- Each service has its own database (good)

**Strengths:**
- Service independence
- Polyglot persistence
- Event-driven for async operations
- Horizontal scalability
- Infrastructure as Code

**Technical Debt:**
- No API versioning
- Inconsistent logging formats
- No distributed tracing
- Missing circuit breakers
- No service mesh
- Shared library creates coupling

**Recommendations:**
- Implement distributed tracing (Jaeger/Zipkin)
- Standardize logging (structured JSON)
- Add circuit breakers (Resilience4j/Hystrix)
- Consider service mesh (Istio/Linkerd)
- Implement API versioning
- Reduce shared library dependencies

---

## Example 2: Discovering a Legacy Monolith

### Scenario
Large Java monolith, 10+ years old, planning modernization.

### Discovery Process

#### Step 1: Repository Analysis (1 hour)

**Structure:**
```
legacy-app/
├── src/main/java/com/company/
│   ├── web/          (Controllers, JSPs)
│   ├── service/      (Business logic)
│   ├── dao/          (Data access)
│   ├── model/        (Entities)
│   └── util/         (Utilities)
├── src/main/resources/
│   ├── applicationContext.xml
│   └── hibernate.cfg.xml
└── pom.xml
```

**Technology stack (from pom.xml):**
```xml
<properties>
  <java.version>1.8</java.version>
  <spring.version>4.3.30</spring.version>
  <hibernate.version>4.3.11</hibernate.version>
</properties>
```
→ Java 8, Spring 4, Hibernate 4 (all outdated)

#### Step 2: Module Identification (2 hours)

**Package analysis:**
```
com.company.web.customer.*     → Customer Management
com.company.web.order.*        → Order Processing
com.company.web.inventory.*    → Inventory Management
com.company.web.billing.*      → Billing
com.company.web.reporting.*    → Reporting
com.company.web.admin.*        → Administration
```

**Dependency analysis:**
```java
// OrderController depends on:
- CustomerService
- InventoryService
- BillingService
- NotificationService
- ReportingService

// High coupling - order processing touches everything!
```

#### Step 3: Database Analysis (1 hour)

**From hibernate.cfg.xml and entity classes:**

```sql
-- 47 tables in single Oracle database:
CUSTOMERS
ADDRESSES
ORDERS
ORDER_ITEMS
PRODUCTS
INVENTORY
INVOICES
PAYMENTS
SHIPMENTS
... (38 more)
```

**Observations:**
- All modules share same database
- No clear schema boundaries
- Some tables used by multiple modules
- Complex foreign key relationships
- Some tables have 50+ columns

#### Step 4: Integration Points (1 hour)

**External integrations found:**

```java
// SOAP web services (legacy)
@WebService
public class LegacyPartnerService {
    // Integration with old partner system
}

// REST endpoints (newer)
@RestController
public class ApiController {
    // Mobile app integration
}

// FTP file transfers
public class BatchFileProcessor {
    // Nightly batch jobs
}

// Direct database access
// Reporting tool connects directly to database (bad!)
```

#### Step 5: Code Quality Analysis (2 hours)

**Findings:**

```java
// God class example:
public class OrderService {
    // 5,247 lines of code!
    // Handles: validation, pricing, inventory, 
    // billing, shipping, notifications, reporting
}

// Tight coupling:
public class CustomerController {
    @Autowired
    private CustomerService customerService;
    @Autowired
    private OrderService orderService;
    @Autowired
    private BillingService billingService;
    @Autowired
    private ReportingService reportingService;
    // ... 8 more dependencies
}

// Hard-coded configuration:
public class EmailService {
    private static final String SMTP_HOST = "mail.company.com";
    private static final String API_KEY = "abc123xyz"; // Security issue!
}
```

**Technical debt identified:**
- God classes (5000+ lines)
- Tight coupling between modules
- Hard-coded configuration
- Secrets in code
- No unit tests
- Outdated dependencies
- Security vulnerabilities

#### Step 6: Architecture Documentation

**Current Architecture:**

```
┌─────────────────────────────────────────┐
│         Presentation Layer              │
│  (JSP, REST Controllers, SOAP Services) │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────▼───────────────────────┐
│         Business Logic Layer            │
│    (Service classes - tightly coupled)  │
└─────────────────┬───────────────────────┘
                  │
┌─────────────────▼───────────────────────┐
│         Data Access Layer               │
│         (Hibernate, Custom SQL)         │
└─────────────────┬───────────────────────┘
                  │
           ┌──────▼──────┐
           │   Oracle DB  │
           └─────────────┘
```

**Module boundaries (logical, not enforced):**

```
Monolith
├── Customer Management
│   ├── Customer CRUD
│   ├── Address management
│   └── Customer preferences
├── Order Processing
│   ├── Order creation
│   ├── Order fulfillment
│   └── Order tracking
├── Inventory Management
│   ├── Stock management
│   ├── Warehouse operations
│   └── Supplier integration
├── Billing
│   ├── Invoice generation
│   ├── Payment processing
│   └── Accounting integration
└── Reporting
    ├── Sales reports
    ├── Inventory reports
    └── Financial reports
```

**Modernization Strategy:**

1. **Phase 1: Stabilize**
   - Add automated tests
   - Update dependencies
   - Fix security vulnerabilities
   - Externalize configuration

2. **Phase 2: Decouple**
   - Introduce interfaces between modules
   - Extract shared utilities
   - Separate database schemas logically

3. **Phase 3: Extract Services**
   - Extract Billing as first microservice (least coupled)
   - Extract Inventory as second service
   - Extract Order Processing (most complex, do last)

4. **Phase 4: Modernize**
   - Upgrade to Java 17, Spring Boot 3
   - Migrate to cloud-native architecture
   - Implement event-driven patterns

---

## Example 3: Discovering a Serverless Application

### Scenario
AWS serverless application, need to understand architecture for adding new features.

### Discovery Process

#### Step 1: Infrastructure Analysis (1 hour)

**From CloudFormation template:**

```yaml
Resources:
  # API Gateway
  ApiGateway:
    Type: AWS::ApiGateway::RestApi
  
  # Lambda Functions (23 total)
  UserAuthFunction:
    Type: AWS::Lambda::Function
    Runtime: nodejs14.x
  
  CreateOrderFunction:
    Type: AWS::Lambda::Function
    Runtime: python3.9
  
  # Data Stores
  UsersTable:
    Type: AWS::DynamoDB::Table
  
  OrdersTable:
    Type: AWS::DynamoDB::Table
  
  FilesBucket:
    Type: AWS::S3::Bucket
  
  # Messaging
  OrderQueue:
    Type: AWS::SQS::Queue
  
  OrderTopic:
    Type: AWS::SNS::Topic
```

**Component inventory:**
- 23 Lambda functions
- API Gateway (REST API)
- 5 DynamoDB tables
- 3 S3 buckets
- 4 SQS queues
- 2 SNS topics
- 1 EventBridge rule (scheduled)

#### Step 2: Function Categorization (1 hour)

**API Functions** (triggered by API Gateway):
```
- UserAuthFunction (POST /auth/login)
- GetUserFunction (GET /users/{id})
- CreateOrderFunction (POST /orders)
- GetOrderFunction (GET /orders/{id})
- ListOrdersFunction (GET /orders)
```

**Event Processing Functions** (triggered by events):
```
- ProcessOrderFunction (SQS trigger)
- SendNotificationFunction (SNS trigger)
- GenerateInvoiceFunction (SNS trigger)
- UpdateInventoryFunction (DynamoDB stream)
```

**Scheduled Functions** (triggered by EventBridge):
```
- DailyReportFunction (daily at 2 AM)
- CleanupOldDataFunction (weekly)
```

**S3 Functions** (triggered by S3 events):
```
- ProcessUploadFunction (on file upload)
- GenerateThumbnailFunction (on image upload)
```

#### Step 3: Data Flow Mapping (2 hours)

**Order creation flow:**

```
1. Client → API Gateway → CreateOrderFunction
2. CreateOrderFunction:
   - Validates request
   - Writes to OrdersTable (DynamoDB)
   - Publishes to OrderTopic (SNS)
3. OrderTopic fans out to:
   - ProcessOrderFunction (via SQS)
   - GenerateInvoiceFunction (direct)
   - SendNotificationFunction (direct)
4. ProcessOrderFunction:
   - Processes payment
   - Updates OrdersTable
   - Publishes to InventoryTopic
5. UpdateInventoryFunction:
   - Triggered by DynamoDB stream
   - Updates InventoryTable
```

**Architecture diagram:**

```
[Client]
   ↓
[API Gateway]
   ↓
[Lambda: CreateOrder]
   ├─→ [DynamoDB: Orders]
   └─→ [SNS: OrderTopic]
         ├─→ [SQS: OrderQueue] → [Lambda: ProcessOrder]
         ├─→ [Lambda: GenerateInvoice] → [S3: Invoices]
         └─→ [Lambda: SendNotification] → [SES]

[DynamoDB: Orders] (stream)
   ↓
[Lambda: UpdateInventory]
   ↓
[DynamoDB: Inventory]
```

#### Step 4: Findings and Recommendations

**Strengths:**
- Fully serverless (no server management)
- Event-driven architecture
- Automatic scaling
- Pay-per-use pricing

**Issues:**
- No X-Ray tracing (hard to debug)
- Inconsistent error handling
- Some functions too large (>1000 lines)
- Cold start issues on some functions
- No centralized logging
- Secrets in environment variables (should use Secrets Manager)

**Recommendations:**
- Enable X-Ray tracing on all functions
- Implement structured logging (JSON)
- Split large functions
- Use provisioned concurrency for critical functions
- Migrate secrets to AWS Secrets Manager
- Add CloudWatch dashboards
- Implement circuit breakers for external calls

---

## Key Takeaways

1. **Start with structure** - Repository and folder organization reveals a lot
2. **Follow the data** - Data flows reveal how the system really works
3. **Talk to the team** - They know things not in the code
4. **Use multiple sources** - Code, configs, running system, docs
5. **Document as you go** - Don't wait until the end
6. **Validate findings** - Review with team to catch misunderstandings
7. **Focus on objectives** - Don't document everything, focus on what matters