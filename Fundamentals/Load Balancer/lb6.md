# 🛡️ High Availability and Fault Tolerance - System Design Fundamentals

## 📋 Table of Contents
- [Core Concepts](#-core-concepts)
- [Redundancy and Failover Strategies](#-redundancy-and-failover-strategies)
- [Health Checks and Monitoring](#-health-checks-and-monitoring)
- [Synchronization and State Sharing](#-synchronization-and-state-sharing)
- [Implementation Patterns](#-implementation-patterns)
- [Best Practices](#-best-practices)
- [Interview-Ready Summary](#-interview-ready-summary)
- [Real-World Examples](#-real-world-examples)

---

## 🎯 Core Concepts

High Availability (HA) and Fault Tolerance are critical for ensuring **reliable, uninterrupted service** even when components fail.

### 🔑 **Key Definitions:**

| Concept | Definition | Goal |
|---------|------------|------|
| **🔄 High Availability** | System remains operational most of the time | Minimize downtime |
| **🛡️ Fault Tolerance** | System continues operating despite failures | Handle failures gracefully |
| **🔁 Redundancy** | Multiple instances of critical components | Eliminate single points of failure |
| **🔄 Failover** | Automatic switching to backup systems | Seamless recovery |

### 📊 **Availability Metrics:**

| Availability | Downtime/Year | Downtime/Month | Use Case |
|--------------|---------------|----------------|----------|
| **99%** | 3.65 days | 7.3 hours | Basic web services |
| **99.9%** | 8.77 hours | 44 minutes | Business applications |
| **99.99%** | 52.6 minutes | 4.3 minutes | E-commerce platforms |
| **99.999%** | 5.26 minutes | 26 seconds | Banking, critical systems |

---

## 🔁 Redundancy and Failover Strategies

### 1. 🎯 Active-Passive Configuration

**How it works:** One load balancer handles traffic (active), another waits on standby (passive)

```
Normal Operation:
Internet → Active Load Balancer → Backend Servers
           Passive Load Balancer (Standby)

Failure Scenario:
Internet → Active Load Balancer (FAILED) 
           Passive Load Balancer → Backend Servers (Takes Over)
```

| Pros ✅ | Cons ❌ |
|---------|---------|
| **Simple setup** - Easy to configure | **Resource waste** - Passive LB sits idle |
| **Clear failover** - Straightforward switching | **Longer failover time** - Detection + switch delay |
| **No split-brain** - One active instance | **50% resource utilization** - Half capacity unused |
| **Reliable** - Well-tested pattern | **Manual intervention** - May need human action |

**🎯 Perfect For:**
- **Cost-sensitive environments** - Don't want complex setup
- **Legacy systems** - Simple, proven approach
- **Low-traffic applications** - Don't need full capacity always

### 2. ⚖️ Active-Active Configuration

**How it works:** Multiple load balancers handle traffic simultaneously

```
Normal Operation:
Internet → DNS/External LB splits traffic
           ↓                    ↓
    Active LB 1           Active LB 2
           ↓                    ↓
    Backend Servers      Backend Servers

Failure Scenario:
Internet → DNS/External LB detects failure
           ↓
    Active LB 1 (FAILED)   Active LB 2 (Handles all traffic)
                                ↓
                         Backend Servers
```

| Pros ✅ | Cons ❌ |
|---------|---------|
| **Full resource utilization** - All LBs active | **Complex setup** - Coordination required |
| **Better performance** - Distributed load | **State synchronization** - Data consistency challenges |
| **Faster failover** - No standby warm-up | **Split-brain risk** - Multiple active instances |
| **Linear scaling** - Add more active instances | **Higher costs** - More infrastructure |

**🎯 Perfect For:**
- **High-traffic applications** - Need maximum capacity
- **Performance-critical systems** - Can't afford reduced capacity
- **Global applications** - Multiple data centers

### 📊 **Configuration Comparison:**

| Aspect | Active-Passive | Active-Active |
|--------|----------------|---------------|
| **Resource Utilization** | 50% | 100% |
| **Failover Time** | 30-60 seconds | 1-5 seconds |
| **Complexity** | Low | High |
| **Cost** | Lower | Higher |
| **Performance** | Medium | High |
| **Risk of Split-Brain** | Low | Medium |

---

## 🏥 Health Checks and Monitoring

### 🔍 **Health Check Types**

#### 1. 🌐 Network-Level Health Checks

**What it checks:** Basic connectivity and response

```
Load Balancer → TCP Connection → Backend Server
Load Balancer ← ACK/Response ← Backend Server
```

| Type | Method | Speed | Accuracy |
|------|--------|-------|----------|
| **TCP Check** | Connect to port | Fast | Basic |
| **HTTP Check** | GET /health | Medium | Good |
| **HTTPS Check** | GET /health (SSL) | Slower | Good |

#### 2. 🎯 Application-Level Health Checks

**What it checks:** Application functionality and dependencies

```
Health Check Request:
GET /health HTTP/1.1
Host: backend-server

Healthy Response:
HTTP/1.1 200 OK
{
  "status": "healthy",
  "database": "connected",
  "cache": "operational",
  "timestamp": "2024-01-15T10:30:00Z"
}

Unhealthy Response:
HTTP/1.1 503 Service Unavailable
{
  "status": "unhealthy",
  "database": "disconnected",
  "cache": "operational"
}
```

#### 3. 🔧 Custom Health Checks

**What it checks:** Business-specific logic and metrics

**Examples:**
- **E-commerce:** Can process orders?
- **Payment system:** Can authorize transactions?
- **Media streaming:** Can serve video content?

### ⚙️ **Health Check Configuration**

| Parameter | Typical Value | Purpose |
|-----------|---------------|---------|
| **Interval** | 10-30 seconds | How often to check |
| **Timeout** | 5-10 seconds | Max time to wait |
| **Healthy Threshold** | 2-3 checks | Consecutive passes to mark healthy |
| **Unhealthy Threshold** | 2-5 checks | Consecutive fails to mark unhealthy |

### 📊 **Monitoring Metrics**

#### 🎯 Load Balancer Metrics

| Metric | Healthy Range | Alert Threshold |
|--------|---------------|-----------------|
| **Response Time** | < 100ms | > 500ms |
| **Error Rate** | < 1% | > 5% |
| **CPU Usage** | < 70% | > 85% |
| **Memory Usage** | < 80% | > 90% |
| **Active Connections** | Baseline ±20% | > 150% baseline |

#### 🎯 Backend Server Metrics

| Metric | Purpose | Action |
|--------|---------|--------|
| **Response Time** | Server performance | Scale if slow |
| **Error Rate** | Application health | Remove if high errors |
| **Connection Count** | Load distribution | Rebalance if uneven |
| **Resource Usage** | Capacity planning | Add servers if high |

### 🚨 **Alerting Strategy**

```
Severity Levels:
P1 (Critical) → Service Down → Page on-call immediately
P2 (High) → Performance Degraded → Notify team leads
P3 (Medium) → Warning Thresholds → Email/Slack alerts
P4 (Low) → Info/Trends → Dashboard monitoring
```

---

## 🔄 Synchronization and State Sharing

### 1. 🏢 Centralized Configuration Management

**Purpose:** Ensure all load balancer instances use consistent settings

#### Popular Solutions:

| Solution | Type | Best For |
|----------|------|----------|
| **etcd** | Key-value store | Kubernetes environments |
| **Consul** | Service mesh | Microservices |
| **ZooKeeper** | Coordination service | Hadoop ecosystem |
| **Redis** | In-memory store | Session data |

**Implementation Example:**
```
Configuration Store (etcd):
{
  "backend_servers": [
    {"ip": "10.0.1.10", "port": 8080, "weight": 100},
    {"ip": "10.0.1.11", "port": 8080, "weight": 100}
  ],
  "health_check": {
    "interval": 30,
    "timeout": 5,
    "path": "/health"
  }
}

Load Balancer 1 ← Watches config changes ← etcd
Load Balancer 2 ← Watches config changes ← etcd
```

### 2. 🔄 State Sharing and Replication

**Purpose:** Synchronize session data and state across load balancer instances

#### Session Storage Strategies:

| Strategy | Implementation | Pros | Cons |
|----------|----------------|------|------|
| **Database Replication** | Master-slave DB setup | Persistent, ACID | Slower, complex |
| **Distributed Cache** | Redis Cluster/Memcached | Fast, scalable | Memory-based |
| **Built-in Sync** | LB vendor solution | Integrated | Vendor lock-in |
| **External Store** | Separate session service | Decoupled | Additional complexity |

#### **Redis Cluster Example:**
```
Session Replication:
LB1 → Redis Master (writes) → Redis Slaves (replication)
LB2 → Redis Master (reads)  → Redis Slaves (replication)

Session Data:
{
  "session_id": "abc123",
  "user_id": "user456",
  "server_assignment": "server2",
  "created_at": "2024-01-15T10:00:00Z",
  "last_accessed": "2024-01-15T10:30:00Z"
}
```

### 3. 📡 State Synchronization Patterns

#### Pattern 1: **Event-Driven Sync**
```
LB1: Server fails → Publish event → Message Queue
LB2: ← Subscribe to events ← Message Queue → Updates local state
```

#### Pattern 2: **Periodic Sync**
```
Every 30 seconds:
LB1 → Sync state → Central Store
LB2 → Fetch state ← Central Store
```

#### Pattern 3: **Real-time Sync**
```
LB1: State change → Real-time sync → LB2
LB1: ← Acknowledgment ← LB2
```

---

## 🛠️ Implementation Patterns

### 1. 🌐 Geographic Redundancy

**Multi-Region Setup:**
```
Global DNS (Route 53) → Routes to healthy regions
    ↓                           ↓
US East Region              EU West Region
    ↓                           ↓
Load Balancer Cluster    Load Balancer Cluster
    ↓                           ↓
Backend Servers          Backend Servers
```

### 2. 🎯 Layered Redundancy

**Multiple Layers of Protection:**
```
Layer 1: DNS Load Balancing (Global)
Layer 2: Hardware Load Balancers (Regional)
Layer 3: Software Load Balancers (Local)
Layer 4: Application Instances (Service)
```

### 3. 🔄 Circuit Breaker Pattern

**Automatic Failure Detection:**
```python
class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failure_count = 0
        self.failure_threshold = failure_threshold
        self.timeout = timeout
        self.state = "CLOSED"  # CLOSED, OPEN, HALF_OPEN
    
    def call_service(self, service_call):
        if self.state == "OPEN":
            if time.now() - self.last_failure > self.timeout:
                self.state = "HALF_OPEN"
            else:
                raise CircuitOpenException()
        
        try:
            result = service_call()
            if self.state == "HALF_OPEN":
                self.state = "CLOSED"
                self.failure_count = 0
            return result
        except Exception:
            self.failure_count += 1
            if self.failure_count >= self.failure_threshold:
                self.state = "OPEN"
                self.last_failure = time.now()
            raise
```

---

## 💡 Best Practices

### 🎯 **Design Principles**

| Principle | Implementation | Benefit |
|-----------|----------------|---------|
| **Redundancy** | Multiple instances of everything | No single points of failure |
| **Graceful Degradation** | Reduce features, maintain core service | Better than complete failure |
| **Fast Failure Detection** | Aggressive health checks | Quick recovery |
| **Automated Recovery** | Self-healing systems | Reduce manual intervention |

### 🔧 **Configuration Best Practices**

#### Health Check Tuning:
```
# Conservative (Slower failover, fewer false positives)
interval: 30s
timeout: 10s
unhealthy_threshold: 5
healthy_threshold: 3

# Aggressive (Faster failover, more false positives)
interval: 5s
timeout: 2s
unhealthy_threshold: 2
healthy_threshold: 2
```

#### Connection Draining:
```
# Graceful server removal
1. Mark server as "draining"
2. Stop sending new requests
3. Wait for existing connections to finish
4. Remove server from pool
```

---

## 🎤 Interview-Ready Summary

### ❓ **"How do you ensure high availability in load balancers?"**

**Framework Answer:**
1. **Redundancy** - Multiple load balancer instances
2. **Health monitoring** - Continuous health checks
3. **Automatic failover** - Active-passive or active-active
4. **State synchronization** - Consistent configuration and session data

### ❓ **"What's the difference between active-passive and active-active?"**

| Aspect | Active-Passive | Active-Active |
|--------|----------------|---------------|
| **Resource Usage** | 50% (one idle) | 100% (all active) |
| **Failover Speed** | Slower (30-60s) | Faster (1-5s) |
| **Complexity** | Simple | Complex |
| **Best For** | Cost-sensitive | Performance-critical |

### ❓ **"How do you handle state in a multi-LB setup?"**

**Solutions:**
1. **Stateless design** - No state to synchronize
2. **External session store** - Redis, database
3. **State replication** - Sync between instances
4. **Sticky sessions** - Client affinity to one LB

### ❓ **"What metrics do you monitor for load balancer health?"**

**Key Metrics:**
- **Response time** - Performance indicator
- **Error rate** - Health indicator
- **Resource usage** - Capacity planning
- **Connection count** - Load distribution
- **Backend health** - Service availability

---

## 🌍 Real-World Examples

### **🎬 Netflix Architecture:**
```
Global Traffic → AWS Route 53 (DNS failover)
                      ↓
Regional Traffic → ELB clusters (Active-Active)
                      ↓
Microservices → Application Load Balancers
                      ↓
Instances → Auto Scaling Groups (Self-healing)
```

**HA Strategy:**
- **Multi-region deployment** - US, EU, Asia
- **Chaos engineering** - Intentionally break things to test
- **Circuit breakers** - Automatic service isolation
- **Real-time monitoring** - Thousands of metrics

### **🛒 Amazon E-commerce:**
```
Customer Request → Global Load Balancer
                      ↓
Regional Data Center → Hardware Load Balancers (HA pair)
                      ↓
Web Tier → Software Load Balancers (Auto-scaling)
                      ↓
App Tier → Microservice Load Balancers
                      ↓
Data Tier → Database Connection Pooling
```

**HA Features:**
- **99.99% availability target** - ~4 minutes downtime/month
- **Active-active configuration** - Multiple data centers
- **Real-time health monitoring** - Sub-second detection
- **Automated failover** - No manual intervention

### **🏦 Banking System:**
```
ATM Network → Geographic Load Balancing
                      ↓
Core Banking → Active-Passive Hardware LBs
                      ↓
Transaction Processing → Stateful Load Balancing
                      ↓
Database → Master-Slave Replication
```

**HA Requirements:**
- **99.999% availability** - 5 minutes downtime/year
- **Zero data loss** - Synchronous replication
- **Instant failover** - Money can't wait
- **Audit trails** - Every transaction logged

---

## 💡 Key Takeaways

### **🎯 Essential Points:**
- **Redundancy eliminates single points of failure**
- **Health checks enable automatic failure detection**
- **State synchronization ensures consistency**
- **Monitoring provides visibility and alerting**

### **🚀 Implementation Strategy:**
1. **Start with basic redundancy** - Active-passive setup
2. **Add comprehensive monitoring** - Health checks and metrics
3. **Implement state management** - Session handling strategy
4. **Evolve to active-active** - When performance demands it

### **🎯 For Interviews:**
- **Know the trade-offs** - Complexity vs reliability vs cost
- **Understand real examples** - How major platforms achieve HA
- **Think about failures** - What happens when things break?
- **Consider monitoring** - How do you know if it's working?

---

## 📚 Source
[Design Gurus - High Availability and Fault Tolerance](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/high-availability-and-fault-tolerance)

## 🏷️ Tags
`#SystemDesign` `#HighAvailability` `#FaultTolerance` `#LoadBalancing` `#Redundancy` `#Monitoring` `#Interview`

## 🔗 Quick Links
- [🔝 Back to Top](#️-high-availability-and-fault-tolerance---system-design-fundamentals)
- [📋 Table of Contents](#-table-of-contents)

---

> **💡 Pro Tip:** Remember the "3 R's" of high availability: **Redundancy** (multiple instances), **Reliability** (health monitoring), and **Recovery** (automated failover). Master these concepts for any system design interview!