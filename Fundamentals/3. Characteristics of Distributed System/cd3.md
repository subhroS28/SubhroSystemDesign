# ⚡ Latency and Performance - System Design Fundamentals

## 📋 Table of Contents
- [Understanding Latency and Performance](#-understanding-latency-and-performance)
- [Data Locality](#-data-locality)
- [Load Balancing for Performance](#-load-balancing-for-performance)
- [Caching Strategies](#-caching-strategies)
- [Performance Optimization Techniques](#-performance-optimization-techniques)
- [Real-World Performance Examples](#-real-world-performance-examples)
- [Interview-Ready Summary](#-interview-ready-summary)

---

## 🎯 Understanding Latency and Performance

**Latency** and **Performance** are critical factors that directly impact user experience and system scalability.

### 🔑 **Core Definitions:**

| Concept | Definition | Measurement | Impact |
|---------|------------|-------------|--------|
| **Latency** | Time taken to process a single request | Milliseconds (ms) | User perceived speed |
| **Throughput** | Number of requests processed per unit time | Requests/second (RPS) | System capacity |
| **Performance** | Overall efficiency of the system | Response time + throughput | Business success |
| **Scalability** | Ability to maintain performance under load | Performance at scale | Growth enablement |

### 📊 **Latency Benchmarks:**

| Application Type | Excellent | Good | Acceptable | Poor |
|-----------------|-----------|------|-----------|------|
| **Web Pages** | < 100ms | 100-300ms | 300-1000ms | > 1000ms |
| **APIs** | < 50ms | 50-200ms | 200-500ms | > 500ms |
| **Databases** | < 10ms | 10-50ms | 50-100ms | > 100ms |
| **CDN** | < 20ms | 20-100ms | 100-300ms | > 300ms |
| **Real-time Gaming** | < 20ms | 20-50ms | 50-100ms | > 100ms |

### 💰 **Business Impact of Performance:**

| Performance Metric | Business Impact | Real Example |
|-------------------|----------------|--------------|
| **100ms delay** | 1% revenue loss | Amazon: $1.6B annual loss |
| **1 second delay** | 7% conversion drop | Google: 20% traffic loss |
| **3 second load time** | 40% users abandon | E-commerce sites |
| **Page speed rank** | SEO impact | Google ranking factor |

---

## 📍 Data Locality

### 🎯 **Core Concept:**
**"Keep data close to where it's needed most"**

### 🔄 **How Data Locality Works:**

```
Without Data Locality:
User in Tokyo → Request to Server in US → Database in Europe
Total latency: 50ms + 100ms + 80ms = 230ms

With Data Locality:
User in Tokyo → Request to Tokyo Server → Tokyo Database  
Total latency: 5ms + 2ms + 3ms = 10ms

Performance Improvement: 23x faster!
```

### 📊 **Data Locality Strategies:**

#### **1. Geographic Data Distribution:**

| Strategy | Implementation | Use Case | Latency Reduction |
|----------|----------------|----------|------------------|
| **Regional Replicas** | Data copied to multiple regions | Global applications | 50-80% |
| **Edge Caching** | Frequent data at edge locations | Content delivery | 70-90% |
| **CDN** | Static content globally distributed | Media, websites | 60-85% |
| **Local Processing** | Compute where data resides | Analytics, ML | 40-70% |

#### **2. Data Partitioning (Sharding):**

```sql
-- Geographic Sharding Example
-- Users table partitioned by region

-- US Users Database
CREATE TABLE users_us (
    user_id INT PRIMARY KEY,
    username VARCHAR(50),
    region ENUM('US') DEFAULT 'US',
    created_at TIMESTAMP
);

-- EU Users Database  
CREATE TABLE users_eu (
    user_id INT PRIMARY KEY,
    username VARCHAR(50), 
    region ENUM('EU') DEFAULT 'EU',
    created_at TIMESTAMP
);

-- Application Logic
function getUserData(userId) {
    const userRegion = determineUserRegion(userId);
    if (userRegion === 'US') {
        return queryUSDatabase(userId);
    } else if (userRegion === 'EU') {
        return queryEUDatabase(userId);
    }
}
```

#### **3. Data Replication Patterns:**

##### **Master-Slave Replication:**
```
Write Operations:
App → Master DB (US-East)
     ↓
Async Replication:
Master → [Slave DB (US-West), Slave DB (EU), Slave DB (Asia)]

Read Operations:
US Users → US-West Slave
EU Users → EU Slave  
Asia Users → Asia Slave

Benefits:
- Writes: Single source of truth
- Reads: Local to users (low latency)
- Consistency: Eventually consistent
```

##### **Multi-Master Replication:**
```
Global Multi-Master Setup:
US Master ←→ EU Master ←→ Asia Master
    ↓           ↓          ↓
US Reads    EU Reads   Asia Reads
US Writes   EU Writes  Asia Writes

Benefits:
- Writes: Local to users
- Reads: Local to users
- Availability: Any master can serve
- Complexity: Conflict resolution needed
```

### 💡 **Real Example - Social Media Platform:**

```
Facebook-Style Data Locality:

User Profile Data:
- Primary location: Where user registered
- Replicated to: Regions where user's friends are
- Cached at: Edge locations user visits frequently

Post Data:
- Stored in: Region where posted
- Replicated to: Friends' regions
- Cached in: Popular content cache

Friend Graph:
- Sharded by: User ID ranges
- Replicated to: User's active regions
- Indexed for: Fast friend lookups

Result:
- 95% of requests served locally
- < 50ms response time globally
- Reduced cross-region traffic by 80%
```

---

## ⚖️ Load Balancing for Performance

### 🎯 **Performance-Focused Load Balancing:**

Load balancing optimizes performance by distributing workload efficiently and routing traffic intelligently.

#### **Performance-Optimized Algorithms:**

| Algorithm | Performance Benefit | Best Use Case |
|-----------|-------------------|---------------|
| **Least Response Time** | Routes to fastest server | Latency-critical apps |
| **Least Connections** | Prevents server overload | Variable request durations |
| **Weighted Round Robin** | Matches server capacity | Mixed server types |
| **Geographic Routing** | Reduces network latency | Global applications |

#### **Advanced Load Balancing for Performance:**

##### **1. Health-Based Routing:**
```nginx
# NGINX Advanced Health Checks
upstream backend_servers {
    # Primary servers (high performance)
    server web1.company.com:8080 weight=5 max_fails=2 fail_timeout=30s;
    server web2.company.com:8080 weight=5 max_fails=2 fail_timeout=30s;
    
    # Backup servers (lower performance)
    server web3.company.com:8080 weight=3 backup;
    server web4.company.com:8080 weight=3 backup;
}

# Health check configuration
location /health {
    access_log off;
    return 200 "healthy\n";
    add_header Content-Type text/plain;
}
```

##### **2. Performance-Based Routing:**
```python
# Custom Load Balancer with Performance Metrics
class PerformanceBasedLoadBalancer:
    def __init__(self):
        self.servers = [
            {'url': 'server1.com', 'response_time': 50, 'cpu_usage': 30},
            {'url': 'server2.com', 'response_time': 80, 'cpu_usage': 60}, 
            {'url': 'server3.com', 'response_time': 120, 'cpu_usage': 90}
        ]
    
    def select_server(self):
        # Calculate performance score (lower is better)
        for server in self.servers:
            server['score'] = (server['response_time'] * 0.7 + 
                             server['cpu_usage'] * 0.3)
        
        # Route to best performing server
        best_server = min(self.servers, key=lambda x: x['score'])
        return best_server['url']
    
    def update_metrics(self, server_url, response_time, cpu_usage):
        for server in self.servers:
            if server['url'] == server_url:
                server['response_time'] = response_time
                server['cpu_usage'] = cpu_usage
```

### 📊 **Load Balancer Performance Optimization:**

#### **Connection Optimization:**
```
Connection Pooling Benefits:
- Without pooling: 100ms per connection setup
- With pooling: 5ms to reuse existing connection
- Improvement: 20x faster connection handling

Keep-Alive Benefits:
- HTTP/1.1: Reuse connection for multiple requests
- Reduces: TCP handshake overhead
- Improves: 30-50% performance gain
```

---

## 💾 Caching Strategies

### 🎯 **Caching for Performance:**

**"Store frequently accessed data in fast storage for quick retrieval"**

### 📊 **Caching Layers:**

| Layer | Storage Type | Access Time | Use Case |
|-------|-------------|-------------|----------|
| **CPU Cache** | On-chip SRAM | 1-10ns | Processor instructions |
| **Memory Cache** | RAM | 50-100ns | Application data |
| **SSD Cache** | Flash storage | 0.1-1ms | Hot data |
| **Network Cache** | Remote memory | 1-10ms | Distributed systems |
| **Disk Cache** | Hard drives | 5-20ms | Cold data |

### 🔄 **Caching Patterns:**

#### **1. Cache-Aside (Lazy Loading):**
```python
def get_user_profile(user_id):
    # Check cache first
    cache_key = f"user_profile:{user_id}"
    cached_data = redis_client.get(cache_key)
    
    if cached_data:
        return json.loads(cached_data)  # Cache hit
    
    # Cache miss - fetch from database
    user_data = database.query(f"SELECT * FROM users WHERE id = {user_id}")
    
    # Store in cache for next time
    redis_client.setex(cache_key, 3600, json.dumps(user_data))  # 1 hour TTL
    
    return user_data

# Performance Impact:
# Cache hit: 5ms response time
# Cache miss: 50ms response time  
# Cache hit ratio: 85% (typical)
# Average response time: (85% × 5ms) + (15% × 50ms) = 11.75ms vs 50ms
```

#### **2. Write-Through Cache:**
```python
def update_user_profile(user_id, new_data):
    # Update database first
    database.update(f"UPDATE users SET ... WHERE id = {user_id}", new_data)
    
    # Update cache immediately
    cache_key = f"user_profile:{user_id}"
    redis_client.setex(cache_key, 3600, json.dumps(new_data))
    
    return "Updated successfully"

# Benefits: Cache always consistent with database
# Drawbacks: Slower writes (both DB and cache updated)
```

#### **3. Write-Behind (Write-Back) Cache:**
```python
def update_user_profile(user_id, new_data):
    # Update cache immediately
    cache_key = f"user_profile:{user_id}"
    redis_client.setex(cache_key, 3600, json.dumps(new_data))
    
    # Schedule async database update
    celery_task.delay('update_database', user_id, new_data)
    
    return "Updated successfully"

# Benefits: Very fast writes (cache only)
# Drawbacks: Risk of data loss if cache fails
```

### 🌐 **Distributed Caching:**

#### **Redis Cluster Example:**
```bash
# Redis Cluster Configuration
redis-cli --cluster create \
    192.168.1.10:7000 192.168.1.11:7000 192.168.1.12:7000 \
    192.168.1.10:7001 192.168.1.11:7001 192.168.1.12:7001 \
    --cluster-replicas 1

# Application Configuration
import redis.sentinel

# Connection to Redis Cluster
r = redis.RedisCluster(
    startup_nodes=[
        {"host": "192.168.1.10", "port": 7000},
        {"host": "192.168.1.11", "port": 7000}, 
        {"host": "192.168.1.12", "port": 7000}
    ],
    decode_responses=True
)

# Sharded data storage
def cache_user_data(user_id, data):
    # Data automatically sharded across cluster nodes
    cache_key = f"user:{user_id}"
    r.setex(cache_key, 3600, json.dumps(data))
```

#### **CDN (Content Delivery Network):**
```javascript
// CloudFlare CDN Configuration
const cdnConfig = {
    // Cache static assets aggressively
    "*.css": {
        cache_ttl: "1 year",
        browser_ttl: "1 year"
    },
    "*.js": {
        cache_ttl: "1 year", 
        browser_ttl: "1 year"
    },
    "*.png|*.jpg|*.gif": {
        cache_ttl: "1 month",
        browser_ttl: "1 week"
    },
    
    // Cache API responses briefly
    "/api/*": {
        cache_ttl: "5 minutes",
        browser_ttl: "1 minute"
    },
    
    // Never cache dynamic content
    "/user/dashboard": {
        cache_ttl: "0",
        browser_ttl: "0"
    }
};

// Performance Impact:
// Static assets: 95% cache hit ratio
// API responses: 60% cache hit ratio
// Overall latency reduction: 70%
```

### 📈 **Cache Performance Optimization:**

#### **Cache Hit Ratio Optimization:**
```
Cache Performance Metrics:

Hit Ratio = Cache Hits / (Cache Hits + Cache Misses)

Performance Impact:
- 50% hit ratio: 50% performance improvement
- 80% hit ratio: 80% performance improvement
- 95% hit ratio: 95% performance improvement

Optimization Strategies:
1. Increase cache size (more data fits)
2. Optimize TTL (Time To Live) settings
3. Implement cache warming (pre-populate)
4. Use better cache keys (avoid conflicts)
```

---

## 🚀 Performance Optimization Techniques

### ⚡ **Database Performance:**

#### **1. Query Optimization:**
```sql
-- Slow Query (300ms)
SELECT u.*, p.*, o.* 
FROM users u 
LEFT JOIN profiles p ON u.id = p.user_id
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.created_at > '2023-01-01';

-- Optimized Query (30ms)
SELECT u.id, u.username, u.email,
       p.first_name, p.last_name,
       COUNT(o.id) as order_count
FROM users u 
LEFT JOIN profiles p ON u.id = p.user_id
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.created_at > '2023-01-01'
  AND u.status = 'active'
GROUP BY u.id, p.first_name, p.last_name;

-- Add appropriate indexes
CREATE INDEX idx_users_created_status ON users(created_at, status);
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

#### **2. Connection Pooling:**
```python
# Database Connection Pool
from sqlalchemy import create_engine
from sqlalchemy.pool import QueuePool

# Configure connection pool
engine = create_engine(
    'postgresql://user:password@localhost/dbname',
    poolclass=QueuePool,
    pool_size=20,          # Number of connections to maintain
    max_overflow=30,       # Additional connections allowed
    pool_pre_ping=True,    # Validate connections
    pool_recycle=3600      # Recycle connections every hour
)

# Performance Benefits:
# Without pooling: 50ms to establish connection + query time
# With pooling: 2ms to get connection + query time
# Improvement: 25x faster connection handling
```

### 🌐 **Network Performance:**

#### **1. HTTP/2 Optimization:**
```nginx
# NGINX HTTP/2 Configuration
server {
    listen 443 ssl http2;
    server_name example.com;
    
    # Enable HTTP/2 push for critical resources
    location = /index.html {
        http2_push /css/main.css;
        http2_push /js/main.js;
        http2_push /images/logo.png;
    }
    
    # Optimize SSL/TLS
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_buffer_size 4k;
}

# Performance Benefits:
# HTTP/1.1: 6 parallel connections, sequential requests
# HTTP/2: Single connection, parallel requests + server push
# Improvement: 40-60% faster page loads
```

#### **2. Compression:**
```nginx
# NGINX Compression Configuration
gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_proxied any;
gzip_comp_level 6;
gzip_types
    text/plain
    text/css
    text/xml
    text/javascript
    application/json
    application/javascript
    application/xml+rss
    application/atom+xml
    image/svg+xml;

# Brotli compression (better than gzip)
brotli on;
brotli_comp_level 6;
brotli_types text/plain text/css application/json application/javascript;

# Performance Impact:
# Uncompressed: 2MB JavaScript file
# Gzip: 600KB (70% reduction)
# Brotli: 500KB (75% reduction)
# Load time: 2s → 0.5s on slow connections
```

### 📱 **Client-Side Performance:**

#### **1. Browser Caching:**
```http
# HTTP Headers for Browser Caching
Cache-Control: public, max-age=31536000, immutable  # 1 year for static assets
Cache-Control: public, max-age=300                  # 5 minutes for API responses
Cache-Control: no-cache                             # Force revalidation
ETag: "33a64df551"                                  # Version-based caching
Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT      # Time-based caching
```

#### **2. Resource Optimization:**
```javascript
// Lazy Loading Images
const imageObserver = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            const img = entry.target;
            img.src = img.dataset.src;
            imageObserver.unobserve(img);
        }
    });
});

document.querySelectorAll('img[data-src]').forEach(img => {
    imageObserver.observe(img);
});

// Code Splitting (Load code when needed)
const loadUserDashboard = async () => {
    const { UserDashboard } = await import('./UserDashboard.js');
    return UserDashboard;
};

// Performance Benefits:
# Initial bundle: 2MB → 500KB (4x smaller)
# Time to interactive: 8s → 2s
# Lazy loaded images: 60% bandwidth savings
```

---

## 🌍 Real-World Performance Examples

### 🛒 **E-commerce Platform Performance:**

```
Amazon-Style Performance Optimization:

Challenge: Handle 100M+ product catalog with sub-100ms response

Solutions Implemented:

1. Multi-Layer Caching:
   ├─ Browser Cache: Static assets (1 year TTL)
   ├─ CDN Cache: Product images (1 month TTL)
   ├─ Application Cache: Product data (1 hour TTL)
   ├─ Database Cache: Query results (15 minutes TTL)
   └─ Search Cache: Search results (5 minutes TTL)

2. Database Optimization:
   ├─ Product data sharded by category
   ├─ Read replicas in each region (3 per region)
   ├─ Search index separate from transactional DB
   └─ Connection pooling (100 connections/server)

3. Content Delivery:
   ├─ 200+ CDN edge locations globally
   ├─ Product images optimized per device
   ├─ Critical CSS/JS inlined
   └─ Lazy loading for below-fold content

Results:
- Page load time: 1.2s (vs 4.5s before)
- Cache hit ratio: 92% overall
- Database query time: 15ms average
- Conversion rate: +23% improvement
- Revenue: +$2.1B annually from performance gains
```

### 🎬 **Video Streaming Performance:**

```
Netflix-Style Streaming Optimization:

Challenge: Stream HD video to 200M+ users globally with minimal buffering

Solutions Implemented:

1. Content Delivery Network:
   ├─ 15,000+ servers in 1,000+ locations
   ├─ Content pre-positioned based on predictions
   ├─ Multiple encoding formats per video
   └─ Adaptive bitrate streaming

2. Caching Strategy:
   ├─ Popular content: Cached at all edge servers
   ├─ Regional content: Cached in relevant regions
   ├─ Long-tail content: Cached on-demand
   └─ Metadata: Cached in memory globally

3. Performance Optimization:
   ├─ Video segments pre-fetched during playback
   ├─ Connection keep-alive for video chunks
   ├─ Smart client buffering (30-60 seconds)
   └─ Fallback servers for failed connections

Results:
- Buffering ratio: < 0.5% globally
- Start-up time: < 2 seconds average
- Content delivery: 99% from edge servers
- Bandwidth efficiency: 40% reduction through optimization
- User satisfaction: 90%+ rating for video quality
```

### 🎮 **Gaming Platform Performance:**

```
Real-Time Gaming Performance:

Challenge: Sub-20ms latency for competitive gaming

Solutions Implemented:

1. Geographic Distribution:
   ├─ Game servers in 50+ locations globally
   ├─ Players matched to nearest server
   ├─ Dedicated game server hardware
   └─ Direct network peering with ISPs

2. Network Optimization:
   ├─ UDP protocol for game state (vs TCP)
   ├─ Custom compression for game packets
   ├─ Predictive movement algorithms
   └─ Client-side lag compensation

3. Server Performance:
   ├─ High-frequency CPUs (3.8GHz+)
   ├─ Low-latency memory (DDR4-3200)
   ├─ NVMe SSD storage for fast loading
   └─ Dedicated network interfaces

Results:
- Average latency: 15ms globally
- 99th percentile latency: < 35ms
- Packet loss: < 0.1%
- Server uptime: 99.95%
- Player satisfaction: 95%+ for connection quality
```

### 🏦 **Financial Trading Performance:**

```
High-Frequency Trading System:

Challenge: Sub-millisecond order execution

Solutions Implemented:

1. Ultra-Low Latency Network:
   ├─ Co-located servers in exchange data centers
   ├─ Dedicated fiber optic connections
   ├─ Custom network hardware (FPGA-based)
   └─ Bypass operating system kernel

2. Hardware Optimization:
   ├─ CPU cores dedicated to trading threads
   ├─ Memory locked in RAM (no swapping)
   ├─ Custom hardware for order matching
   └─ GPU acceleration for risk calculations

3. Software Optimization:
   ├─ Assembly language for critical paths
   ├─ Memory pre-allocation (no garbage collection)
   ├─ Lock-free data structures
   └─ Single-threaded event loops

Results:
- Order latency: 50 microseconds average
- Market data processing: 10 million messages/second
- System jitter: < 5 microseconds
- Uptime: 99.999% (5 minutes/year downtime)
- Revenue: Latency improvements = $100M+ annually
```

---

## 🎤 Interview-Ready Summary

### ❓ **"How do you optimize system performance and reduce latency?"**

**Framework Answer (3-Layer Approach):**

1. **Data Layer Optimization:**
   - Data locality (keep data close to users)
   - Database indexing and query optimization
   - Caching strategies (multi-layer caching)
   - Data partitioning and replication

2. **Application Layer Optimization:**
   - Load balancing with performance-aware routing
   - Connection pooling and keep-alive
   - Asynchronous processing
   - Code optimization and profiling

3. **Network Layer Optimization:**
   - CDN for static content delivery
   - HTTP/2 and compression
   - Geographic distribution
   - Network protocol optimization

### ❓ **"What's the difference between latency and throughput?"**

**Key Differences:**
- **Latency**: Time to process ONE request (measured in milliseconds)
- **Throughput**: Number of requests processed per second (measured in RPS)
- **Analogy**: Highway traffic
  - Latency = Time for one car to travel from A to B
  - Throughput = Number of cars passing a point per hour

**Trade-offs:**
- Sometimes optimizing for one can hurt the other
- High throughput might increase individual request latency
- Ultra-low latency might reduce overall system throughput

### ❓ **"How do you implement effective caching?"**

**Caching Strategy Framework:**
1. **Identify what to cache**: Frequently accessed, computation-heavy data
2. **Choose cache layer**: Memory, distributed, CDN based on needs
3. **Select cache pattern**: Cache-aside, write-through, write-behind
4. **Set appropriate TTL**: Balance freshness vs performance
5. **Monitor metrics**: Hit ratio, miss penalty, eviction rate

**Common Pitfalls:**
- Caching dynamic/personalized data
- Not handling cache invalidation properly
- Cache stampede during cache misses
- Over-caching leading to memory pressure

### ❓ **"How do you measure and improve system performance?"**

**Performance Measurement:**
- **Response time percentiles** (P50, P95, P99)
- **Throughput** under various load levels
- **Error rates** during high traffic
- **Resource utilization** (CPU, memory, network)

**Improvement Process:**
1. **Profile and measure** current performance
2. **Identify bottlenecks** using monitoring tools
3. **Optimize iteratively** starting with biggest impact
4. **Load test** to validate improvements
5. **Monitor continuously** to prevent regressions

---

## 💡 Key Takeaways

### **🎯 Essential Points:**
- **Performance directly impacts business** - 100ms delay = 1% revenue loss
- **Multi-layer optimization needed** - Data, application, and network layers
- **Caching is crucial** - Can provide 10-100x performance improvements
- **Geographic distribution matters** - Physics limits of network latency
- **Measure everything** - You can't optimize what you don't measure

### **🚀 Performance Optimization Priority:**
1. **Caching** - Highest impact, lowest effort for most applications
2. **Database optimization** - Indexes, query optimization, connection pooling
3. **CDN** - For global applications with static content
4. **Load balancing** - Distribute load effectively
5. **Network optimization** - HTTP/2, compression, keep-alive

### **🎯 For Interviews:**
- **Know the business impact** - Connect performance to revenue
- **Understand trade-offs** - Latency vs throughput, consistency vs performance
- **Think in layers** - Multiple optimization strategies working together
- **Use real numbers** - Specific latency targets and performance improvements

### **⚡ Performance Rules of Thumb:**
- **Caching can improve performance 10-100x**
- **Database queries should be < 10ms on average**
- **Web pages should load in < 3 seconds**
- **APIs should respond in < 100ms**
- **Every 100ms delay reduces conversions by ~1%**

---

## 📚 Source
[Design Gurus - Latency and Performance](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/latency-and-performance)

## 🏷️ Tags
`#SystemDesign` `#Latency` `#Performance` `#Caching` `#Optimization` `#DataLocality` `#Interview`

## 🔗 Quick Links
- [🔝 Back to Top](#-latency-and-performance---system-design-fundamentals)
- [📋 Table of Contents](#-table-of-contents)

---

> **💡 Pro Tip:** In system design interviews, always discuss performance optimization in terms of business impact. Show that you understand how technical improvements translate to better user experience and increased revenue. Use specific numbers and real-world examples!