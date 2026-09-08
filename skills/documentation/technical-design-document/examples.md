# Technical Design Document - Examples

## Example 1: Real-Time Notification System

### Executive Summary

**Problem**: Users are not notified in real-time about important events (new messages, mentions, system alerts). Current email-only notifications have 15-minute delay and low engagement (20% open rate).

**Solution**: Build a real-time notification system supporting web push, mobile push, in-app notifications, and email. Deliver notifications within 1 second of event occurrence.

**Benefits**:
- Improve user engagement (target: 60% notification interaction rate)
- Reduce time-to-action for critical alerts
- Enable real-time collaboration features

**Timeline**: 8 weeks (2 engineers)

**Resources**: $500/month additional infrastructure cost

---

### Requirements

#### Functional Requirements

1. **Notification Types**:
   - In-app notifications (toast, notification center)
   - Web push notifications (browser)
   - Mobile push notifications (iOS, Android via FCM)
   - Email notifications (fallback)

2. **Event Sources**:
   - New message in conversation
   - Mention in comment
   - Task assigned
   - System alerts (maintenance, security)

3. **User Preferences**:
   - Enable/disable each notification type
   - Quiet hours (no notifications during specified times)
   - Notification frequency (immediate, batched)

4. **Notification Management**:
   - Mark as read/unread
   - Archive notifications
   - View notification history (last 30 days)

#### Non-Functional Requirements

1. **Performance**:
   - Deliver notifications within 1 second of event
   - Support 10,000 concurrent connections (WebSocket)
   - Handle 1,000 notifications/second

2. **Scalability**:
   - Scale to 100,000 users
   - Handle traffic spikes (10x normal load)

3. **Reliability**:
   - 99.9% availability
   - No lost notifications (at-least-once delivery)
   - Graceful degradation if push services unavailable

4. **Security**:
   - Authenticate all WebSocket connections
   - Encrypt notification content
   - Respect user privacy settings

---

### Scope

#### In Scope
- Real-time notification delivery system
- WebSocket server for in-app notifications
- Integration with FCM for mobile push
- Integration with web push API
- Notification preferences UI
- Notification center UI

#### Out of Scope
- Email notification system (already exists)
- SMS notifications (future phase)
- Slack/Teams integrations (future phase)
- Advanced notification routing rules (future phase)

#### Goals
- 60% notification interaction rate (vs. 20% for email)
- < 1 second notification delivery latency
- 99.9% notification delivery success rate

#### Non-Goals
- Not replacing email notifications (complementary)
- Not building a general-purpose messaging system
- Not supporting offline notification queuing (rely on push services)

---

### High-Level Architecture

```
┌─────────────┐
│   Clients   │
│ (Web/Mobile)│
└──────┬──────┘
       │ WebSocket (in-app)
       │ FCM (mobile push)
       │ Web Push API (browser)
       ↓
┌──────────────────────────────────────┐
│     Notification Gateway (Node.js)    │
│  - WebSocket server                   │
│  - Push notification sender           │
│  - Connection management              │
└──────────────┬───────────────────────┘
               │
               ↓
┌──────────────────────────────────────┐
│   Notification Service (Python)       │
│  - Event processing                   │
│  - User preference filtering          │
│  - Notification formatting            │
└──────────────┬───────────────────────┘
               │
               ↓
┌──────────────────────────────────────┐
│       Message Queue (RabbitMQ)        │
│  - Event ingestion                    │
│  - Delivery guarantees                │
└──────────────┬───────────────────────┘
               │
               ↑
┌──────────────────────────────────────┐
│    Application Services               │
│  (Chat, Comments, Tasks, etc.)        │
│  - Publish notification events        │
└───────────────────────────────────────┘

┌──────────────────────────────────────┐
│   PostgreSQL (Notification Store)     │
│  - Notification history               │
│  - User preferences                   │
│  - Read/unread status                 │
└───────────────────────────────────────┘

┌──────────────────────────────────────┐
│   Redis (Connection State)            │
│  - Active WebSocket connections       │
│  - User online status                 │
└───────────────────────────────────────┘
```

#### Component Responsibilities

**Notification Gateway (Node.js)**:
- Maintain WebSocket connections with clients
- Send real-time notifications to connected clients
- Send push notifications via FCM and Web Push API
- Track connection state in Redis

**Notification Service (Python)**:
- Consume events from RabbitMQ
- Filter notifications based on user preferences
- Format notifications for different channels
- Store notifications in PostgreSQL
- Publish to Notification Gateway

**Message Queue (RabbitMQ)**:
- Ingest notification events from application services
- Ensure at-least-once delivery
- Handle backpressure during traffic spikes

**PostgreSQL**:
- Store notification history (30 days)
- Store user preferences
- Store read/unread status

**Redis**:
- Track active WebSocket connections
- Store user online status
- Cache user preferences

---

### Detailed Component Design

#### Notification Gateway API

**WebSocket Connection**:
```
WS /notifications/stream
Authorization: Bearer <jwt_token>

// Client → Server (subscribe)
{
  "type": "subscribe",
  "user_id": "user_123"
}

// Server → Client (notification)
{
  "type": "notification",
  "id": "notif_456",
  "event_type": "new_message",
  "title": "New message from Alice",
  "body": "Hey, are you available for a call?",
  "data": {
    "conversation_id": "conv_789",
    "message_id": "msg_012"
  },
  "timestamp": "2026-09-08T10:30:00Z"
}

// Client → Server (mark as read)
{
  "type": "mark_read",
  "notification_id": "notif_456"
}
```

**REST API**:
```
GET /api/notifications
  - Get notification history (paginated)
  - Query params: page, limit, unread_only

PATCH /api/notifications/:id
  - Mark notification as read/unread

DELETE /api/notifications/:id
  - Archive notification

GET /api/notifications/preferences
  - Get user notification preferences

PUT /api/notifications/preferences
  - Update user notification preferences
```

#### Data Model

**notifications table**:
```sql
CREATE TABLE notifications (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  event_type VARCHAR(50) NOT NULL,
  title VARCHAR(255) NOT NULL,
  body TEXT,
  data JSONB,
  read BOOLEAN DEFAULT FALSE,
  archived BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW(),
  read_at TIMESTAMP,
  INDEX idx_user_created (user_id, created_at DESC),
  INDEX idx_user_unread (user_id, read) WHERE read = FALSE
);
```

**notification_preferences table**:
```sql
CREATE TABLE notification_preferences (
  user_id UUID PRIMARY KEY REFERENCES users(id),
  in_app_enabled BOOLEAN DEFAULT TRUE,
  web_push_enabled BOOLEAN DEFAULT TRUE,
  mobile_push_enabled BOOLEAN DEFAULT TRUE,
  email_enabled BOOLEAN DEFAULT TRUE,
  quiet_hours_start TIME,
  quiet_hours_end TIME,
  frequency VARCHAR(20) DEFAULT 'immediate', -- immediate, batched
  updated_at TIMESTAMP DEFAULT NOW()
);
```

**push_subscriptions table**:
```sql
CREATE TABLE push_subscriptions (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID NOT NULL REFERENCES users(id),
  type VARCHAR(20) NOT NULL, -- web_push, fcm
  endpoint TEXT NOT NULL,
  keys JSONB, -- for web push
  device_token TEXT, -- for FCM
  created_at TIMESTAMP DEFAULT NOW(),
  UNIQUE(user_id, type, endpoint)
);
```

#### Sequence Diagram: New Message Notification

```
Chat Service    RabbitMQ    Notification    Notification    Client
                            Service         Gateway         (WebSocket)
     │              │            │               │               │
     │ Publish      │            │               │               │
     │ new_message  │            │               │               │
     │─────────────>│            │               │               │
     │              │            │               │               │
     │              │ Consume    │               │               │
     │              │───────────>│               │               │
     │              │            │               │               │
     │              │            │ Filter by     │               │
     │              │            │ preferences   │               │
     │              │            │               │               │
     │              │            │ Format        │               │
     │              │            │ notification  │               │
     │              │            │               │               │
     │              │            │ Store in DB   │               │
     │              │            │               │               │
     │              │            │ Send to       │               │
     │              │            │ Gateway       │               │
     │              │            │──────────────>│               │
     │              │            │               │               │
     │              │            │               │ Send via      │
     │              │            │               │ WebSocket     │
     │              │            │               │──────────────>│
     │              │            │               │               │
     │              │            │               │ Send via      │
     │              │            │               │ FCM (if       │
     │              │            │               │ offline)      │
     │              │            │               │               │
```

---

### Design Decisions

#### ADR-001: Use WebSocket for Real-Time Delivery

**Context**: Need to deliver notifications to web clients in real-time.

**Alternatives**:
1. **WebSocket** - Bidirectional, real-time, persistent connection
2. **Server-Sent Events (SSE)** - Unidirectional, real-time, simpler
3. **Long Polling** - HTTP-based, higher latency, more overhead

**Decision**: Use WebSocket

**Rationale**:
- Bidirectional communication allows clients to send acknowledgments
- Lower latency than long polling
- Better support for mobile apps (can reuse for React Native)
- Team has experience with Socket.IO

**Consequences**:
- Need to handle WebSocket connection state (reconnection, heartbeat)
- Need load balancer with sticky sessions or Redis pub/sub for multi-instance
- Slightly more complex than SSE

---

#### ADR-002: Use RabbitMQ for Event Ingestion

**Context**: Need a message queue to decouple event producers from notification service.

**Alternatives**:
1. **RabbitMQ** - Feature-rich, at-least-once delivery, mature
2. **AWS SQS** - Fully managed, simpler, vendor lock-in
3. **Kafka** - High throughput, overkill for our scale

**Decision**: Use RabbitMQ

**Rationale**:
- At-least-once delivery guarantees (no lost notifications)
- Already used in our infrastructure
- Sufficient throughput for our scale (1,000 msg/sec)
- No vendor lock-in

**Consequences**:
- Need to manage RabbitMQ cluster (operational overhead)
- Need to handle duplicate notifications (idempotency)

---

#### ADR-003: Store Notifications in PostgreSQL

**Context**: Need to store notification history for 30 days.

**Alternatives**:
1. **PostgreSQL** - Relational, ACID, already used
2. **MongoDB** - Flexible schema, good for JSON data
3. **DynamoDB** - Fully managed, auto-scaling

**Decision**: Use PostgreSQL

**Rationale**:
- Already used for user data (no new infrastructure)
- JSONB support for flexible notification data
- Sufficient performance for our scale (10K users)
- ACID guarantees for read/unread status

**Consequences**:
- Need to partition or archive old notifications
- Need indexes for efficient queries (user_id, created_at)

---

### Non-Functional Requirements

#### Performance

**Latency**:
- Event → Notification delivery: < 1 second (p95)
- API response time: < 200ms (p95)

**Throughput**:
- 1,000 notifications/second (peak)
- 10,000 concurrent WebSocket connections

**Optimizations**:
- Redis caching for user preferences
- Connection pooling for database
- Horizontal scaling of Notification Gateway

#### Scalability

**Current Scale**: 10,000 users
**Target Scale**: 100,000 users

**Scaling Strategy**:
- **Notification Gateway**: Horizontal scaling (stateless with Redis for connection state)
- **Notification Service**: Horizontal scaling (stateless workers)
- **RabbitMQ**: Cluster with 3 nodes
- **PostgreSQL**: Read replicas for notification history queries
- **Redis**: Cluster mode for connection state

**Capacity Estimates**:
- 100K users × 10 notifications/day = 1M notifications/day
- Peak: 1,000 notifications/second
- Storage: 1M notifications/day × 30 days × 1KB = 30GB/month

#### Reliability

**Availability**: 99.9% (43 minutes downtime/month)

**Redundancy**:
- Notification Gateway: 3+ instances behind load balancer
- Notification Service: 2+ worker instances
- RabbitMQ: 3-node cluster
- PostgreSQL: Primary + read replica
- Redis: Cluster with replication

**Failure Handling**:
- WebSocket disconnection → Client auto-reconnects with exponential backoff
- RabbitMQ down → Events queued in application services, retry
- FCM/Web Push API down → Graceful degradation, retry with exponential backoff
- Database down → Notifications delivered in real-time, history unavailable

**At-Least-Once Delivery**:
- RabbitMQ message acknowledgment after successful delivery
- Idempotency: Deduplicate notifications by (user_id, event_type, event_id)

#### Security

**Authentication**:
- WebSocket: JWT token in connection request
- REST API: JWT token in Authorization header

**Authorization**:
- Users can only access their own notifications
- Verify user_id in JWT matches requested notifications

**Data Protection**:
- TLS 1.3 for all connections
- Encrypt sensitive notification data in database (PII)
- Respect user privacy settings (quiet hours, disabled channels)

**Rate Limiting**:
- 100 API requests/minute per user
- 10 WebSocket connections per user

#### Observability

**Metrics**:
- Notification delivery latency (p50, p95, p99)
- Notification delivery success rate
- WebSocket connection count
- RabbitMQ queue depth
- Database query latency

**Logs**:
- Structured logs (JSON) with request_id
- Log levels: DEBUG (dev), INFO (prod), ERROR (always)
- Centralized logging (ELK stack)

**Tracing**:
- Distributed tracing (Jaeger) for notification flow
- Trace from event publish to client delivery

**Dashboards**:
- System health: Notification delivery rate, latency, errors
- WebSocket connections: Active connections, connection churn
- RabbitMQ: Queue depth, message rate, consumer lag

**Alerts**:
- Notification delivery latency > 5 seconds (p95)
- Notification delivery success rate < 95%
- RabbitMQ queue depth > 10,000
- WebSocket connection errors > 100/minute

---

### Risks and Mitigations

**Risk 1: WebSocket Connection Storms**
- **Likelihood**: Medium
- **Impact**: High (server overload)
- **Scenario**: All clients reconnect simultaneously after network outage
- **Mitigation**: 
  - Implement exponential backoff with jitter on client
  - Rate limit WebSocket connections
  - Auto-scale Notification Gateway based on connection count

**Risk 2: Notification Spam**
- **Likelihood**: Medium
- **Impact**: Medium (user annoyance, unsubscribe)
- **Scenario**: Bug in application service sends duplicate events
- **Mitigation**:
  - Idempotency: Deduplicate by (user_id, event_type, event_id)
  - Rate limiting: Max 100 notifications/hour per user
  - Monitoring: Alert on notification rate spikes

**Risk 3: FCM/Web Push API Downtime**
- **Likelihood**: Low
- **Impact**: Medium (mobile/web push unavailable)
- **Scenario**: Third-party push service outage
- **Mitigation**:
  - Graceful degradation: In-app notifications still work
  - Retry with exponential backoff (up to 1 hour)
  - Fallback to email for critical notifications

**Risk 4: Database Growth**
- **Likelihood**: High
- **Impact**: Medium (storage cost, query performance)
- **Scenario**: Notification history grows beyond expected
- **Mitigation**:
  - Auto-archive notifications older than 30 days
  - Partition table by month
  - Monitor storage usage, alert at 80% capacity

---

### Implementation Plan

#### Phase 1: Core Infrastructure (Week 1-2)
- Set up RabbitMQ cluster
- Set up Redis cluster
- Create database schema
- Build Notification Service (event consumer, preference filtering)
- **Deliverable**: Notification Service consuming events and storing in DB

#### Phase 2: WebSocket Gateway (Week 3-4)
- Build Notification Gateway (WebSocket server)
- Implement connection management with Redis
- Implement real-time delivery
- **Deliverable**: In-app notifications working end-to-end

#### Phase 3: Push Notifications (Week 5-6)
- Integrate FCM for mobile push
- Integrate Web Push API for browser push
- Build push subscription management
- **Deliverable**: Mobile and web push notifications working

#### Phase 4: UI and Preferences (Week 7-8)
- Build notification center UI
- Build notification preferences UI
- Implement quiet hours and frequency settings
- **Deliverable**: Full user experience complete

#### Testing Strategy
- **Unit tests**: 80% code coverage
- **Integration tests**: End-to-end notification flow
- **Load testing**: 10,000 concurrent connections, 1,000 notifications/sec
- **Chaos testing**: Simulate RabbitMQ, Redis, database failures

#### Rollout Strategy
- **Week 7**: Internal beta (engineering team)
- **Week 8**: Limited beta (10% of users)
- **Week 9**: Full rollout (100% of users)
- **Rollback**: Feature flag to disable new notification system, fallback to email

---

## Example 2: Payment Processing System (Outline)

### Executive Summary
- **Problem**: Need to process credit card payments for e-commerce platform
- **Solution**: Integrate with Stripe, build payment orchestration layer
- **Benefits**: Accept payments, reduce fraud, improve conversion
- **Timeline**: 6 weeks

### Requirements
- Accept credit cards (Visa, Mastercard, Amex)
- Support 3D Secure for fraud prevention
- Handle refunds and chargebacks
- PCI-DSS compliance (using Stripe)
- 99.99% payment success rate

### High-Level Architecture
```
Checkout UI → Payment Service → Stripe API
                    ↓
              PostgreSQL (payment records)
                    ↓
              Webhook Handler (payment status updates)
```

### Key Design Decisions
- **ADR-001**: Use Stripe (vs. Braintree, Adyen) - Best developer experience, pricing
- **ADR-002**: Store payment metadata only (not card data) - PCI compliance
- **ADR-003**: Async payment confirmation via webhooks - Handle network failures

### Risks
- **Risk**: Stripe API downtime → **Mitigation**: Retry with exponential backoff, queue payments
- **Risk**: Webhook delivery failures → **Mitigation**: Webhook retry mechanism, manual reconciliation

---

## Example 3: Search Service (Outline)

### Executive Summary
- **Problem**: Users can't find content effectively (SQL LIKE queries too slow)
- **Solution**: Build full-text search with Elasticsearch
- **Benefits**: Fast search (< 100ms), relevance ranking, filters
- **Timeline**: 4 weeks

### Requirements
- Full-text search across documents, comments, users
- Autocomplete suggestions
- Filters (date, author, tags)
- Search results < 100ms (p95)
- Support 1,000 searches/second

### High-Level Architecture
```
Search API → Elasticsearch Cluster
                    ↑
            Indexing Service (sync from PostgreSQL)
```

### Key Design Decisions
- **ADR-001**: Use Elasticsearch (vs. Algolia, Typesense) - Self-hosted, cost-effective
- **ADR-002**: Async indexing (vs. sync) - Eventual consistency acceptable
- **ADR-003**: Separate search index per entity type - Simpler relevance tuning

### Risks
- **Risk**: Index lag during high write volume → **Mitigation**: Monitor lag, scale indexing workers
- **Risk**: Elasticsearch cluster failure → **Mitigation**: Fallback to SQL search (degraded)

---

**End of Examples**