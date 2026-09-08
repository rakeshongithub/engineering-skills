# System Design - Practical Examples

## Example 1: URL Shortener Service

### Requirements

**Functional:**
- Users can shorten long URLs
- Users can access original URL via short URL
- Track click analytics
- Custom short URLs (optional)

**Non-Functional:**
- 100M URLs shortened per month
- 10:1 read-to-write ratio
- < 100ms redirect latency (P95)
- 99.9% availability
- URLs never expire

### System Design

#### Architecture Style
**Chosen:** Microservices with caching

**Rationale:**
- Separate scaling for read (redirect) vs. write (shorten)
- High read volume benefits from aggressive caching

#### Components

```
1. API Service (Node.js)
   - Handle URL shortening requests
   - Generate short codes
   - Validate URLs

2. Redirect Service (Go)
   - Handle redirects
   - Optimized for low latency
   - Heavy caching

3. Analytics Service (Python)
   - Track clicks
   - Process analytics
   - Generate reports

4. Database (PostgreSQL)
   - Store URL mappings
   - Store analytics data

5. Cache (Redis)
   - Cache URL mappings
   - Reduce database load
```

#### Data Model

```sql
CREATE TABLE urls (
  id BIGSERIAL PRIMARY KEY,
  short_code VARCHAR(10) UNIQUE NOT NULL,
  original_url TEXT NOT NULL,
  user_id BIGINT,
  created_at TIMESTAMP DEFAULT NOW(),
  INDEX idx_short_code (short_code)
);

CREATE TABLE clicks (
  id BIGSERIAL PRIMARY KEY,
  short_code VARCHAR(10) NOT NULL,
  clicked_at TIMESTAMP DEFAULT NOW(),
  user_agent TEXT,
  ip_address INET,
  referrer TEXT,
  INDEX idx_short_code_time (short_code, clicked_at)
);
```

#### API Design

**Shorten URL:**
```http
POST /api/shorten
Content-Type: application/json

Request:
{
  "url": "https://example.com/very/long/url",
  "custom_code": "mylink" // optional
}

Response (201):
{
  "short_url": "https://short.ly/abc123",
  "short_code": "abc123",
  "original_url": "https://example.com/very/long/url"
}
```

**Redirect:**
```http
GET /{short_code}

Response (301):
Location: https://example.com/very/long/url
```

**Analytics:**
```http
GET /api/analytics/{short_code}

Response (200):
{
  "short_code": "abc123",
  "total_clicks": 1523,
  "clicks_by_day": [...],
  "top_referrers": [...],
  "top_countries": [...]
}
```

#### Short Code Generation

**Algorithm:**
```python
import hashlib
import base62

def generate_short_code(url, id):
    # Use auto-increment ID for uniqueness
    # Base62 encode (0-9, a-z, A-Z)
    return base62.encode(id)
    # Result: 1 → "1", 62 → "10", 1000000 → "4c92"
```

**Length calculation:**
- 62^6 = 56 billion unique codes (6 characters)
- 62^7 = 3.5 trillion unique codes (7 characters)
- Use 7 characters for safety

#### Caching Strategy

**Cache-Aside Pattern:**
```python
def get_original_url(short_code):
    # Try cache first
    url = redis.get(f"url:{short_code}")
    if url:
        return url
    
    # Cache miss - query database
    url = db.query("SELECT original_url FROM urls WHERE short_code = ?", short_code)
    
    # Store in cache (TTL: 24 hours)
    redis.setex(f"url:{short_code}", 86400, url)
    
    return url
```

**Cache warming:**
- Pre-populate cache with popular URLs
- Use analytics to identify hot URLs

#### Scalability Design

**Read Path (Redirects):**
- Horizontal scaling of Redirect Service
- Redis cluster for distributed caching
- Database read replicas
- CDN for static content

**Write Path (Shortening):**
- Horizontal scaling of API Service
- Database connection pooling
- Async analytics processing

**Capacity Planning:**
```
Writes: 100M/month = 40 writes/sec average, 400 writes/sec peak
Reads: 1B/month = 400 reads/sec average, 4000 reads/sec peak

Database:
- 100M URLs × 500 bytes = 50 GB/year
- 1B clicks × 200 bytes = 200 GB/year
- Total: ~250 GB/year

Cache:
- Cache top 20% of URLs (Pareto principle)
- 20M URLs × 500 bytes = 10 GB
```

#### Deployment Architecture

```
[CloudFlare CDN]
       ↓
[Load Balancer]
       │
       ├──────────────────────────────────────────┐
       │                                                │
[Redirect Service] (10 instances)         [API Service] (5 instances)
       │                                                │
       └───────────────┬────────────────────────────────┘
                      │
              [Redis Cluster] (3 nodes)
                      │
          [PostgreSQL Primary]
                      │
          [PostgreSQL Replicas] (2 nodes)
```

---

## Example 2: Real-Time Chat Application

### Requirements

**Functional:**
- One-on-one messaging
- Group chats (up to 100 members)
- Message history
- Online/offline status
- Typing indicators
- Read receipts

**Non-Functional:**
- 1M daily active users
- < 100ms message delivery (P95)
- 99.95% availability
- Messages stored for 1 year
- Support web and mobile clients

### System Design

#### Architecture Style
**Chosen:** Microservices with WebSocket

#### Components

```
1. API Gateway
   - Route HTTP requests
   - Authentication

2. WebSocket Service (Node.js)
   - Maintain persistent connections
   - Real-time message delivery
   - Presence management

3. Message Service (Go)
   - Store messages
   - Message history
   - Search

4. User Service (Java)
   - User profiles
   - Contacts
   - Authentication

5. Notification Service (Python)
   - Push notifications
   - Email notifications

6. Message Queue (RabbitMQ)
   - Async message processing
   - Fanout for group chats

7. Databases:
   - PostgreSQL (users, metadata)
   - Cassandra (messages - time-series)
   - Redis (presence, typing indicators)
```

#### Data Model

**Messages (Cassandra):**
```cql
CREATE TABLE messages (
  conversation_id UUID,
  message_id TIMEUUID,
  sender_id UUID,
  content TEXT,
  created_at TIMESTAMP,
  PRIMARY KEY (conversation_id, message_id)
) WITH CLUSTERING ORDER BY (message_id DESC);
```

**Users (PostgreSQL):**
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY,
  username VARCHAR(50) UNIQUE,
  email VARCHAR(255) UNIQUE,
  created_at TIMESTAMP
);

CREATE TABLE conversations (
  id UUID PRIMARY KEY,
  type VARCHAR(20), -- 'direct' or 'group'
  created_at TIMESTAMP
);

CREATE TABLE conversation_members (
  conversation_id UUID,
  user_id UUID,
  joined_at TIMESTAMP,
  PRIMARY KEY (conversation_id, user_id)
);
```

#### Message Flow

**Sending a Message:**
```
1. User A types message in client
2. Client sends via WebSocket to WebSocket Service
3. WebSocket Service:
   a. Validates message
   b. Publishes to Message Queue
   c. Stores in Cassandra (Message Service)
4. Message Queue:
   a. Fanout to all conversation members
5. WebSocket Service:
   a. Delivers to connected users (User B)
   b. If user offline, Notification Service sends push notification
6. User B receives message in real-time
```

**Sequence Diagram:**
```
User A          WebSocket       Message      Message     WebSocket       User B
Client          Service         Queue        Service     Service         Client
  │               │               │            │           │               │
  ├───send msg───►│               │            │           │               │
  │               ├───publish──►│            │           │               │
  │               ├────store───────────────►│           │               │
  │               │               ├───fanout──────────────────►│               │
  │               │               │            │           ├──deliver──►│
  │               │               │            │           │               │
```

#### WebSocket Connection Management

**Connection:**
```javascript
// Client connects
const ws = new WebSocket('wss://chat.example.com/ws');

// Server authenticates
ws.on('connection', (socket) => {
  const token = socket.handshake.auth.token;
  const userId = authenticateToken(token);
  
  // Store connection
  connections.set(userId, socket);
  
  // Update presence
  redis.set(`presence:${userId}`, 'online');
  redis.publish('presence', { userId, status: 'online' });
});
```

**Scaling WebSocket:**
- Multiple WebSocket server instances
- Redis Pub/Sub for cross-instance messaging
- Sticky sessions (user always connects to same instance)

#### Presence and Typing Indicators

**Presence (Redis):**
```python
# User comes online
redis.setex(f"presence:{user_id}", 300, "online")  # 5 min TTL

# Heartbeat every 60 seconds to keep alive
redis.expire(f"presence:{user_id}", 300)

# Check if user is online
is_online = redis.exists(f"presence:{user_id}")
```

**Typing Indicators (Redis):**
```python
# User starts typing
redis.setex(f"typing:{conversation_id}:{user_id}", 5, "1")  # 5 sec TTL

# Broadcast to conversation members
redis.publish(f"conversation:{conversation_id}", {
  "type": "typing",
  "user_id": user_id
})
```

#### Scalability

**Horizontal Scaling:**
- WebSocket Service: Scale based on connection count
- Message Service: Scale based on write throughput
- Cassandra: Add nodes for storage and throughput

**Database Sharding:**
- Cassandra: Partition by conversation_id
- Messages distributed across cluster

**Caching:**
- Cache recent messages (last 100 per conversation)
- Cache user profiles
- Cache conversation metadata

#### Reliability

**Message Delivery Guarantees:**
- At-least-once delivery
- Client-side deduplication (message_id)
- Retry with exponential backoff

**Failure Handling:**
- WebSocket disconnect: Client auto-reconnects
- Message Service down: Queue messages, process when back
- Database down: Fail gracefully, retry

---

## Example 3: Video Streaming Platform

### Requirements

**Functional:**
- Upload videos
- Transcode to multiple resolutions
- Adaptive bitrate streaming
- Video search
- Comments and likes
- User subscriptions

**Non-Functional:**
- 10M videos
- 100M daily video views
- Support 4K, 1080p, 720p, 480p
- < 2 second video start time
- 99.9% availability

### System Design

#### Architecture Style
**Chosen:** Microservices + Event-Driven + CDN

#### Components

```
1. Upload Service
   - Handle video uploads
   - Store in object storage (S3)
   - Trigger transcoding

2. Transcoding Service
   - Transcode videos to multiple formats
   - Generate thumbnails
   - AWS Elastic Transcoder or FFmpeg

3. Streaming Service
   - Serve video streams
   - HLS/DASH protocols
   - CDN integration

4. Metadata Service
   - Video metadata (title, description, etc.)
   - Search indexing
   - PostgreSQL + Elasticsearch

5. User Service
   - User profiles
   - Subscriptions
   - Authentication

6. Engagement Service
   - Likes, comments, views
   - Analytics

7. CDN (CloudFront)
   - Distribute video content globally
   - Cache video segments
```

#### Video Upload Flow

```
1. User uploads video → Upload Service
2. Upload Service → S3 (raw video)
3. Upload Service → Publishes VideoUploaded event
4. Transcoding Service (triggered by event):
   a. Downloads raw video from S3
   b. Transcodes to multiple resolutions
   c. Generates HLS/DASH manifests
   d. Uploads to S3
   e. Publishes VideoReady event
5. Metadata Service (triggered by VideoReady):
   a. Updates video status to "ready"
   b. Indexes for search
```

#### Video Streaming

**HLS (HTTP Live Streaming):**
```
Video segmented into small chunks (2-10 seconds)

Manifest file (playlist.m3u8):
#EXTM3U
#EXT-X-STREAM-INF:BANDWIDTH=800000,RESOLUTION=640x360
360p/playlist.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=1400000,RESOLUTION=1280x720
720p/playlist.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=2800000,RESOLUTION=1920x1080
1080p/playlist.m3u8
```

**Adaptive Bitrate:**
- Client measures bandwidth
- Switches to appropriate resolution
- Seamless quality adjustment

#### Storage Architecture

**S3 Structure:**
```
videos/
  {video_id}/
    raw/
      original.mp4
    transcoded/
      360p/
        segment_001.ts
        segment_002.ts
        playlist.m3u8
      720p/
        segment_001.ts
        ...
      1080p/
        ...
      master.m3u8
    thumbnails/
      thumb_001.jpg
      thumb_002.jpg
```

**Storage Calculation:**
```
Average video: 10 minutes
Raw 1080p: ~1 GB
Transcoded (all resolutions): ~500 MB
Total per video: ~1.5 GB

10M videos × 1.5 GB = 15 PB
```

#### CDN Strategy

**CloudFront Distribution:**
- Origin: S3 bucket
- Cache behavior: Cache video segments (24 hours)
- Geographic distribution: Edge locations worldwide
- Signed URLs for access control

**Cache Hit Ratio:**
- Popular videos: 95%+ cache hit
- Long-tail videos: 60-70% cache hit
- Reduces S3 costs significantly

#### Scalability

**Upload:**
- Direct upload to S3 (presigned URLs)
- Bypasses application servers

**Transcoding:**
- Distributed workers (Kubernetes)
- Auto-scaling based on queue depth
- Parallel processing of resolutions

**Streaming:**
- CDN handles majority of traffic
- Origin servers scale for cache misses

**Database:**
- PostgreSQL for metadata (sharded by video_id)
- Elasticsearch for search (distributed cluster)
- Redis for caching (hot video metadata)

---

## Key Takeaways

1. **Start with requirements** - Understand before designing
2. **Choose appropriate architecture** - Monolith, microservices, serverless based on needs
3. **Design for scale** - Consider caching, sharding, replication
4. **Plan for failure** - Retries, circuit breakers, graceful degradation
5. **Document trade-offs** - Every decision has pros and cons
6. **Use proven patterns** - Don't reinvent the wheel
7. **Think about operations** - Monitoring, debugging, deployment