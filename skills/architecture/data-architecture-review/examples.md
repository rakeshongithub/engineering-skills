# Data Architecture Review - Examples

## Example 1: E-commerce Platform Data Model Review

### Context
- **System**: E-commerce platform with products, orders, customers
- **Database**: PostgreSQL
- **Scale**: 100K products, 10K orders/day, 1M customers
- **Problem**: Slow product search, slow order history queries

### Data Model Issues Found

#### Critical Issue: Missing Indexes

**Problem**: No indexes on frequently queried columns

```sql
-- ❌ BEFORE: No indexes
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  customer_id INTEGER,  -- No index!
  status VARCHAR(20),   -- No index!
  created_at TIMESTAMP,  -- No index!
  total_amount DECIMAL(10,2)
);

-- Slow query: Find customer's orders
SELECT * FROM orders WHERE customer_id = 12345;  -- Seq scan!
-- Execution time: 2.5 seconds
```

**Recommendation**: Add indexes on foreign keys and frequently queried columns

```sql
-- ✅ AFTER: With indexes
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  customer_id INTEGER,
  status VARCHAR(20),
  created_at TIMESTAMP,
  total_amount DECIMAL(10,2)
);

CREATE INDEX idx_orders_customer_id ON orders(customer_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created_at ON orders(created_at DESC);

-- Fast query: Find customer's orders
SELECT * FROM orders WHERE customer_id = 12345;  -- Index scan
-- Execution time: 15ms
```

**Impact**: 99% query time reduction

#### High Priority: Over-Normalization

**Problem**: Product search requires 5 joins

```sql
-- ❌ BEFORE: Over-normalized
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255)
);

CREATE TABLE product_categories (
  product_id INTEGER,
  category_id INTEGER
);

CREATE TABLE categories (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100)
);

CREATE TABLE product_prices (
  product_id INTEGER,
  price DECIMAL(10,2),
  currency VARCHAR(3)
);

CREATE TABLE product_inventory (
  product_id INTEGER,
  quantity INTEGER
);

-- Complex query with 5 joins
SELECT 
  p.id, p.name, c.name as category, 
  pr.price, i.quantity
FROM products p
JOIN product_categories pc ON p.id = pc.product_id
JOIN categories c ON pc.category_id = c.id
JOIN product_prices pr ON p.id = pr.product_id
JOIN product_inventory i ON p.id = i.product_id
WHERE c.name = 'Electronics';
-- Execution time: 500ms
```

**Recommendation**: Denormalize for read-heavy product catalog

```sql
-- ✅ AFTER: Denormalized for reads
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255),
  category_id INTEGER,
  category_name VARCHAR(100),  -- Denormalized
  price DECIMAL(10,2),         -- Denormalized
  currency VARCHAR(3),
  quantity INTEGER,            -- Denormalized
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);

CREATE INDEX idx_products_category_id ON products(category_id);

-- Simple query, no joins
SELECT id, name, category_name, price, quantity
FROM products
WHERE category_id = 5;  -- Electronics category
-- Execution time: 25ms
```

**Impact**: 95% query time reduction

**Tradeoff**: Must update category_name when category is renamed (rare operation)

#### Medium Priority: Wrong Data Types

**Problem**: Using VARCHAR for numeric values

```sql
-- ❌ BEFORE: Wrong data types
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  sku VARCHAR(50),        -- Should be VARCHAR, OK
  price VARCHAR(20),      -- Should be DECIMAL!
  quantity VARCHAR(10),   -- Should be INTEGER!
  weight VARCHAR(10),     -- Should be DECIMAL!
  is_active VARCHAR(5)    -- Should be BOOLEAN!
);
```

**Recommendation**: Use appropriate data types

```sql
-- ✅ AFTER: Correct data types
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  sku VARCHAR(50) NOT NULL UNIQUE,
  price DECIMAL(10,2) NOT NULL CHECK (price >= 0),
  quantity INTEGER NOT NULL DEFAULT 0 CHECK (quantity >= 0),
  weight DECIMAL(8,2) CHECK (weight > 0),
  is_active BOOLEAN NOT NULL DEFAULT true
);
```

**Benefits**:
- Type safety (can't insert "abc" as price)
- Storage efficiency (INTEGER is 4 bytes vs. VARCHAR 10 bytes)
- Query optimization (database can optimize numeric comparisons)
- Constraints (CHECK ensures price >= 0)

---

## Example 2: Social Media Platform - Choosing Database Technology

### Context
- **System**: Social media platform with posts, comments, likes, follows
- **Scale**: 10M users, 100M posts, 1B likes
- **Requirements**: 
  - Real-time feed generation
  - Full-text search on posts
  - Graph queries (friend recommendations)
  - High write volume (likes, comments)

### Storage Technology Recommendations

#### Use Case 1: User Profiles and Posts

**Requirement**: Flexible schema, nested comments, high read volume

**Recommendation**: MongoDB (Document Store)

```javascript
// ✅ Document model
{
  "_id": ObjectId("..."),
  "userId": "user123",
  "username": "john_doe",
  "content": "Hello world!",
  "createdAt": ISODate("2024-01-01T00:00:00Z"),
  "likes": 42,
  "comments": [  // Embedded comments
    {
      "userId": "user456",
      "username": "jane_smith",
      "content": "Nice post!",
      "createdAt": ISODate("2024-01-01T00:05:00Z")
    }
  ],
  "tags": ["hello", "world"]
}
```

**Why MongoDB**:
- Flexible schema (posts can have different fields)
- Nested comments (no joins needed)
- Horizontal scaling (sharding by userId)
- Fast reads (denormalized data)

#### Use Case 2: Social Graph (Follows, Friends)

**Requirement**: Complex relationship queries, friend recommendations

**Recommendation**: Neo4j (Graph Database)

```cypher
// ✅ Graph model
CREATE (u1:User {id: 'user123', name: 'John'})
CREATE (u2:User {id: 'user456', name: 'Jane'})
CREATE (u1)-[:FOLLOWS]->(u2)

// Friend recommendations: Friends of friends
MATCH (me:User {id: 'user123'})-[:FOLLOWS]->(friend)-[:FOLLOWS]->(fof)
WHERE NOT (me)-[:FOLLOWS]->(fof) AND me <> fof
RETURN fof.name, COUNT(*) as mutual_friends
ORDER BY mutual_friends DESC
LIMIT 10;
```

**Why Neo4j**:
- Optimized for graph queries
- Fast traversals (friend of friend)
- Pattern matching (find influencers)
- Relationship-first data model

#### Use Case 3: Real-time Feed

**Requirement**: Fast reads, TTL, high write volume

**Recommendation**: Redis (Key-Value Store)

```javascript
// ✅ Redis model
// Pre-computed feed for each user
key: "feed:user123"
value: [
  "post:abc123",  // Post IDs, sorted by timestamp
  "post:def456",
  "post:ghi789"
]
TTL: 3600  // Expire after 1 hour

// Fetch feed
const postIds = await redis.lrange('feed:user123', 0, 19);  // Top 20
const posts = await Post.find({ _id: { $in: postIds } });  // Fetch from MongoDB
```

**Why Redis**:
- Extremely fast reads (<1ms)
- TTL for automatic expiration
- List data structure for ordered feeds
- Pub/sub for real-time updates

#### Use Case 4: Full-Text Search

**Requirement**: Search posts by keywords, hashtags, users

**Recommendation**: Elasticsearch (Search Engine)

```javascript
// ✅ Elasticsearch index
{
  "postId": "abc123",
  "userId": "user123",
  "username": "john_doe",
  "content": "Hello world! #introduction",
  "hashtags": ["introduction"],
  "createdAt": "2024-01-01T00:00:00Z",
  "likes": 42
}

// Search query
GET /posts/_search
{
  "query": {
    "multi_match": {
      "query": "introduction",
      "fields": ["content", "hashtags", "username"]
    }
  },
  "sort": [
    { "likes": "desc" },
    { "createdAt": "desc" }
  ]
}
```

**Why Elasticsearch**:
- Full-text search with relevance scoring
- Faceted search (filter by hashtags, users)
- Aggregations (trending hashtags)
- Fast search (<50ms)

#### Use Case 5: Analytics (Likes, Views, Engagement)

**Requirement**: High write volume, time-series data, aggregations

**Recommendation**: ClickHouse (Column Store)

```sql
-- ✅ ClickHouse table
CREATE TABLE post_events (
  event_time DateTime,
  event_type Enum('view', 'like', 'comment', 'share'),
  post_id String,
  user_id String,
  device_type String
) ENGINE = MergeTree()
PARTITION BY toYYYYMM(event_time)
ORDER BY (event_time, post_id);

-- Analytics query: Top posts today
SELECT 
  post_id,
  countIf(event_type = 'view') as views,
  countIf(event_type = 'like') as likes,
  countIf(event_type = 'comment') as comments
FROM post_events
WHERE event_time >= today()
GROUP BY post_id
ORDER BY likes DESC
LIMIT 100;
```

**Why ClickHouse**:
- Optimized for analytics queries
- Handles billions of events
- Fast aggregations
- Automatic partitioning by time

### Polyglot Persistence Architecture

```
┌─────────────┐
│   Client    │
└──────┬──────┘
       │
┌──────▼──────────────────────────────────────┐
│           API Gateway                        │
└──┬────┬────┬────┬────┬────────────────────┘
   │    │    │    │    │
   │    │    │    │    └──▶ ClickHouse (Analytics)
   │    │    │    └───────▶ Elasticsearch (Search)
   │    │    └────────────▶ Redis (Feed Cache)
   │    └─────────────────▶ Neo4j (Social Graph)
   └──────────────────────▶ MongoDB (Posts, Users)
```

**Data Synchronization**:
- MongoDB → Elasticsearch: Change streams
- MongoDB → Redis: Event-driven cache invalidation
- MongoDB → ClickHouse: Kafka event stream

---

## Example 3: Time-Series Data - IoT Sensor Platform

### Context
- **System**: IoT platform collecting sensor data
- **Scale**: 100K devices, 1M events/sec, 1 year retention
- **Data**: Temperature, humidity, pressure readings
- **Queries**: Recent readings, aggregations, anomaly detection

### Database Choice: TimescaleDB vs. Cassandra

#### Option 1: TimescaleDB (PostgreSQL Extension)

```sql
-- ✅ TimescaleDB schema
CREATE TABLE sensor_data (
  time TIMESTAMPTZ NOT NULL,
  device_id TEXT NOT NULL,
  temperature DOUBLE PRECISION,
  humidity DOUBLE PRECISION,
  pressure DOUBLE PRECISION
);

-- Convert to hypertable (automatic partitioning by time)
SELECT create_hypertable('sensor_data', 'time');

-- Automatic compression for old data
ALTER TABLE sensor_data SET (
  timescaledb.compress,
  timescaledb.compress_segmentby = 'device_id'
);

SELECT add_compression_policy('sensor_data', INTERVAL '7 days');

-- Automatic data retention
SELECT add_retention_policy('sensor_data', INTERVAL '1 year');

-- Query: Average temperature per device, last 24 hours
SELECT 
  device_id,
  time_bucket('1 hour', time) AS hour,
  AVG(temperature) as avg_temp
FROM sensor_data
WHERE time > NOW() - INTERVAL '24 hours'
GROUP BY device_id, hour
ORDER BY hour DESC;
```

**Pros**:
- SQL interface (familiar)
- Automatic compression (10x storage reduction)
- Automatic partitioning and retention
- Fast aggregations with continuous aggregates
- ACID transactions

**Cons**:
- Single-node write bottleneck (can scale reads with replicas)
- More expensive than Cassandra at massive scale

#### Option 2: Cassandra

```sql
-- ✅ Cassandra schema
CREATE TABLE sensor_data (
  device_id TEXT,
  day DATE,
  time TIMESTAMP,
  temperature DOUBLE,
  humidity DOUBLE,
  pressure DOUBLE,
  PRIMARY KEY ((device_id, day), time)
) WITH CLUSTERING ORDER BY (time DESC)
  AND compaction = {
    'class': 'TimeWindowCompactionStrategy',
    'compaction_window_size': 1,
    'compaction_window_unit': 'DAYS'
  }
  AND default_time_to_live = 31536000;  -- 1 year

-- Query: Recent readings for device
SELECT * FROM sensor_data
WHERE device_id = 'device123'
  AND day = '2024-01-01'
  AND time > '2024-01-01 00:00:00'
ORDER BY time DESC
LIMIT 100;
```

**Pros**:
- Massive write scalability (linear scaling)
- Automatic TTL (data expiration)
- Multi-datacenter replication
- No single point of failure

**Cons**:
- No joins or aggregations (need to pre-aggregate)
- Eventual consistency
- More complex operations (no SQL)
- Requires careful data modeling

### Recommendation: TimescaleDB

**Rationale**:
- 1M events/sec is within TimescaleDB capacity
- SQL interface simplifies development
- Automatic compression reduces storage costs
- Aggregation queries are common (TimescaleDB excels)
- Team is familiar with PostgreSQL

**If scale increases to 10M+ events/sec**: Migrate to Cassandra

---

## Example 4: Data Migration - Monolith to Microservices

### Context
- **Current**: Single PostgreSQL database for monolith
- **Goal**: Split into microservices with separate databases
- **Challenge**: Maintain data consistency during migration

### Migration Strategy

#### Phase 1: Identify Bounded Contexts

```
Monolith Database:
- users (id, email, name, ...)
- products (id, name, price, inventory, ...)
- orders (id, user_id, status, ...)
- order_items (id, order_id, product_id, quantity, ...)
- payments (id, order_id, amount, status, ...)
```

**Bounded Contexts**:
1. **User Service**: users
2. **Product Service**: products
3. **Order Service**: orders, order_items
4. **Payment Service**: payments

#### Phase 2: Extract Databases

```
┌─────────────────────────────────────┐
│      Monolith Database              │
│  ┌───────┬─────────┬────────┬─────┐│
│  │ users │products │ orders │ ... ││
│  └───────┴─────────┴────────┴─────┘│
└─────────────────────────────────────┘
                 │
                 ▼
┌────────┐  ┌─────────┐  ┌────────┐  ┌─────────┐
│ User   │  │ Product │  │ Order  │  │ Payment │
│   DB   │  │   DB    │  │   DB   │  │   DB    │
└────────┘  └─────────┘  └────────┘  └─────────┘
```

#### Phase 3: Handle Foreign Keys

**Problem**: `orders.user_id` references `users.id` (different databases)

```sql
-- ❌ BEFORE: Foreign key in same database
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id),  -- Can't reference other DB!
  status VARCHAR(20)
);
```

**Solution**: Remove foreign key, enforce in application

```sql
-- ✅ AFTER: No foreign key
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL,  -- No FK, validated in app
  status VARCHAR(20)
);

CREATE INDEX idx_orders_user_id ON orders(user_id);
```

```javascript
// Application-level validation
async function createOrder(userId, items) {
  // Validate user exists (call User Service)
  const user = await userService.getUser(userId);
  if (!user) {
    throw new Error('User not found');
  }
  
  // Create order
  const order = await Order.create({ userId, items });
  return order;
}
```

#### Phase 4: Handle Joins

**Problem**: Can't join across databases

```sql
-- ❌ BEFORE: Join across tables
SELECT 
  o.id, o.status, u.email, u.name
FROM orders o
JOIN users u ON o.user_id = u.id
WHERE o.status = 'pending';
```

**Solution 1**: Denormalize (store user email in orders)

```sql
-- ✅ Denormalized
CREATE TABLE orders (
  id SERIAL PRIMARY KEY,
  user_id INTEGER NOT NULL,
  user_email VARCHAR(255),  -- Denormalized
  user_name VARCHAR(255),   -- Denormalized
  status VARCHAR(20)
);
```

**Solution 2**: Application-level join

```javascript
// ✅ Application-level join
const orders = await Order.find({ status: 'pending' });
const userIds = orders.map(o => o.userId);
const users = await userService.getUsers(userIds);  // Batch API call

const ordersWithUsers = orders.map(order => ({
  ...order,
  user: users.find(u => u.id === order.userId)
}));
```

#### Phase 5: Handle Transactions

**Problem**: Can't use database transactions across services

```sql
-- ❌ BEFORE: Single transaction
BEGIN;
  INSERT INTO orders (user_id, status) VALUES (123, 'pending');
  UPDATE products SET inventory = inventory - 1 WHERE id = 456;
  INSERT INTO payments (order_id, amount) VALUES (1, 99.99);
COMMIT;
```

**Solution**: Saga pattern (compensating transactions)

```javascript
// ✅ Saga pattern
async function createOrderSaga(userId, productId, quantity) {
  let orderId, paymentId;
  
  try {
    // Step 1: Create order
    const order = await orderService.createOrder(userId, productId, quantity);
    orderId = order.id;
    
    // Step 2: Reserve inventory
    await productService.reserveInventory(productId, quantity);
    
    // Step 3: Process payment
    const payment = await paymentService.processPayment(orderId, amount);
    paymentId = payment.id;
    
    // Step 4: Confirm order
    await orderService.confirmOrder(orderId);
    
    return order;
  } catch (error) {
    // Compensating transactions (rollback)
    if (paymentId) {
      await paymentService.refundPayment(paymentId);
    }
    if (orderId) {
      await productService.releaseInventory(productId, quantity);
      await orderService.cancelOrder(orderId);
    }
    throw error;
  }
}
```

### Migration Timeline

**Month 1**: Extract User Service
**Month 2**: Extract Product Service  
**Month 3**: Extract Order Service  
**Month 4**: Extract Payment Service  
**Month 5**: Decommission monolith database

**Risk Mitigation**:
- Dual writes during migration (write to both old and new DB)
- Gradual traffic migration (1% → 10% → 50% → 100%)
- Rollback plan for each phase
- Data consistency validation
