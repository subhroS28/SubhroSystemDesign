# 🔒 Availability - System Design Fundamentals

## 📋 Table of Contents
- [What is Availability?](#-what-is-availability)
- [High Availability Definition](#-high-availability-definition)
- [Availability Metrics](#-availability-metrics)
- [Strategies for High Availability](#-strategies-for-high-availability)
- [Consistency Models](#-consistency-models)
- [Real-World Examples](#-real-world-examples)
- [Interview-Ready Summary](#-interview-ready-summary)

---

## 🎯 What is Availability?

**Availability** is a measure of how accessible and reliable a system is to its users over time.

### 🔑 **Core Definition:**

| Aspect | Description | Example |
|--------|-------------|---------|
| **Accessibility** | System is reachable when needed | Website loads when users visit |
| **Reliability** | System works correctly when accessed | Database returns correct data |
| **Uptime** | Percentage of time system is operational | 99.9% = 8.77 hours downtime/year |
| **Business Impact** | Directly affects revenue and reputation | Downtime costs millions for major platforms |

### 🏗️ **Simple Analogy:**

**Think of availability like a 24/7 convenience store:**

| Store Scenario | System Scenario | Impact |
|----------------|-----------------|--------|
| **Always Open** | 99.99% uptime | Happy customers, high revenue |
| **Occasionally Closed** | 95% uptime | Lost customers, poor reputation |
| **Frequently Closed** | 90% uptime | Business failure |

### 💰 **Business Impact of Downtime:**

| Company | Cost per Hour | Annual Revenue Impact |
|---------|---------------|---------------------|
| **Amazon** | $34 million | Prime Day outage = $100M+ loss |
| **Google** | $2.5 million | Search downtime affects billions |
| **Facebook** | $2.3 million | Social media outage = user exodus |
| **Netflix** | $1.8 million | Streaming outage = subscriber churn |

---

## 🎯 High Availability Definition

### 📊 **Availability Measurement:**

**Formula:** `Availability = Uptime / (Uptime + Downtime) × 100%`

### 🕒 **Common Availability Targets:**

| Availability % | Downtime per Year | Downtime per Month | Downtime per Day | Use Case |
|----------------|-------------------|-------------------|------------------|----------|
| **90%** | 36.53 days | 73 hours | 2.4 hours | **Development systems** |
| **95%** | 18.26 days | 36 hours | 1.2 hours | **Internal tools** |
| **99%** | 3.65 days | 7.3 hours | 14.4 minutes | **Basic web services** |
| **99.9%** | 8.77 hours | 44 minutes | 1.4 minutes | **Business applications** |
| **99.99%** | 52.6 minutes | 4.3 minutes | 8.6 seconds | **E-commerce platforms** |
| **99.999%** | 5.26 minutes | 26 seconds | 0.86 seconds | **Banking, trading systems** |

### 🎯 **The "Nines" Language:**

```
Two Nines (99%): Basic availability
Three Nines (99.9%): Good availability  
Four Nines (99.99%): High availability
Five Nines (99.999%): Very high availability
Six Nines (99.9999%): Extreme availability (rare)
```

### 💡 **Real Example - E-commerce Impact:**

```
Online Store Revenue: $10M/year
99% Availability vs 99.9% Availability:

99% Availability:
- Downtime: 3.65 days/year
- Lost Revenue: $100,000/year
- Customer Trust: Damaged

99.9% Availability:  
- Downtime: 8.77 hours/year
- Lost Revenue: $10,000/year
- Customer Trust: Maintained

Investment in Higher Availability:
- Cost: $50,000/year
- Savings: $90,000/year
- ROI: 180%
```

---

## 📊 Availability Metrics

### 🔢 **Key Performance Indicators:**

| Metric | Formula | Purpose | Good Target |
|--------|---------|---------|-------------|
| **MTBF** | Mean Time Between Failures | Reliability measure | > 1000 hours |
| **MTTR** | Mean Time To Recovery | Recovery speed | < 30 minutes |
| **RTO** | Recovery Time Objective | Max acceptable downtime | < 15 minutes |
| **RPO** | Recovery Point Objective | Max acceptable data loss | < 5 minutes |

### 📈 **Availability Calculation Examples:**

#### **Example 1: Web Application**
```
January Metrics:
- Total time: 744 hours (31 days)
- Planned maintenance: 4 hours
- Unplanned outages: 2 hours
- Total downtime: 6 hours
- Uptime: 738 hours

Availability = 738 / 744 × 100% = 99.19%
```

#### **Example 2: Database System**
```
Monthly Metrics:
- MTBF: 500 hours
- MTTR: 30 minutes (0.5 hours)
- Number of failures: 1

Availability = MTBF / (MTBF + MTTR) × 100%
Availability = 500 / (500 + 0.5) × 100% = 99.9%
```

---

## 🛡️ Strategies for High Availability

### 1. 🔄 Redundancy and Replication

**Core Principle:** *"Never have a single point of failure"*

#### **Types of Redundancy:**

| Type | Implementation | Example |
|------|----------------|---------|
| **Active-Active** | Multiple systems handling traffic | 2 load balancers both processing requests |
| **Active-Passive** | Primary system + standby backup | Database master with hot standby |
| **N+1 Redundancy** | N systems needed + 1 spare | 4 web servers when only 3 needed |
| **Geographic Redundancy** | Systems in different locations | Data centers in multiple cities |

#### **Real Example - Netflix Architecture:**
```
Content Delivery Redundancy:
├─ Regional Data Centers (3 per region)
├─ CDN Edge Servers (15,000+ globally) 
├─ Content Replicas (Multiple copies per region)
└─ Streaming Servers (Auto-scaling groups)

Redundancy Layers:
1. If 1 server fails → Traffic routes to others
2. If 1 data center fails → Route to other DCs
3. If 1 region fails → Route to different region
4. If CDN fails → Fallback to origin servers

Result: 99.9%+ availability for 200M+ subscribers
```

#### **Database Replication Example:**
```mysql
# MySQL Master-Slave Replication
# Master Database (Writes)
CREATE USER 'repl_user'@'%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE ON *.* TO 'repl_user'@'%';

# Slave Database (Reads)  
CHANGE MASTER TO
  MASTER_HOST='master-db.company.com',
  MASTER_USER='repl_user',
  MASTER_PASSWORD='password';
START SLAVE;

# Application Logic
if (operation == 'write') {
    connect to master database
} else {
    connect to slave database (load balanced)
}
```

---

### 2. ⚖️ Load Balancing for Availability

**Purpose:** Distribute load to prevent any single server from becoming overwhelmed

#### **Load Balancing Strategies:**

| Strategy | Availability Benefit | Implementation |
|----------|-------------------|----------------|
| **Health Checks** | Remove failed servers automatically | Check `/health` endpoint every 30s |
| **Graceful Degradation** | Reduce features vs complete failure | Disable search when DB slow |
| **Circuit Breakers** | Stop cascade failures | Open circuit after 5 failures |
| **Failover** | Automatic switching to backup | Switch to standby in 10 seconds |

#### **Health Check Implementation:**
```yaml
# Load Balancer Health Check Configuration
health_check:
  enabled: true
  interval: 10s
  timeout: 3s
  healthy_threshold: 2
  unhealthy_threshold: 3
  path: "/health"
  expected_codes: "200"
  
# Application Health Endpoint
GET /health
{
  "status": "healthy",
  "database": "connected",
  "cache": "operational", 
  "disk_space": "87% available",
  "memory": "65% used",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

---

### 3. 💾 Distributed Data Storage

**Purpose:** Ensure data availability across multiple locations

#### **Data Distribution Strategies:**

| Strategy | Description | Availability Benefit |
|----------|-------------|-------------------|
| **Sharding** | Split data across multiple DBs | No single DB failure affects all data |
| **Replication** | Copy data to multiple locations | Data available from multiple sources |
| **Backup & Recovery** | Regular data backups | Can restore from recent backup |
| **Geographic Distribution** | Data in multiple regions | Survive regional disasters |

#### **Real Example - Cassandra Cluster:**
```
Cassandra Ring Architecture:
Data Centers: US-East, US-West, EU-West

Replication Strategy:
- Replication Factor: 3
- Each data written to 3 nodes
- Nodes distributed across data centers

User Data Distribution:
User ID 12345:
├─ Primary: Node A (US-East)
├─ Replica 1: Node F (US-West)  
└─ Replica 2: Node K (EU-West)

Availability Scenarios:
- 1 node fails: Data available from 2 others
- 1 data center fails: Data available from 2 DCs
- Network partition: Each DC can serve local users
```

#### **Database Backup Strategy:**
```bash
# Automated Backup Script
#!/bin/bash

# Full backup daily
mysqldump --all-databases --single-transaction \
  --routines --triggers > backup_$(date +%Y%m%d).sql

# Incremental backup every hour  
mysqlbinlog --start-datetime="$(date -d '1 hour ago' '+%Y-%m-%d %H:00:00')" \
  mysql-bin.* > incremental_$(date +%Y%m%d_%H).sql

# Test backup restoration weekly
mysql < backup_$(date -d '7 days ago' +%Y%m%d).sql

# Backup retention policy
find /backups -name "backup_*.sql" -mtime +30 -delete
```

---

### 4. 📊 Health Monitoring and Alerts

**Purpose:** Proactive identification and resolution of issues

#### **Monitoring Layers:**

| Layer | What to Monitor | Alert Thresholds |
|-------|----------------|------------------|
| **Infrastructure** | CPU, Memory, Disk, Network | CPU > 85%, Memory > 90% |
| **Application** | Response time, Error rate | Response > 500ms, Errors > 1% |
| **Business** | User signups, Revenue | Signups drop 50%, Revenue drop 25% |
| **Security** | Failed logins, Suspicious activity | 100 failed logins/minute |

#### **Comprehensive Monitoring Stack:**
```yaml
# Prometheus Configuration
global:
  scrape_interval: 15s
  evaluation_interval: 15s

rule_files:
  - "alert_rules.yml"

scrape_configs:
  - job_name: 'web-servers'
    static_configs:
      - targets: ['web1:8080', 'web2:8080', 'web3:8080']
  
  - job_name: 'databases'  
    static_configs:
      - targets: ['db1:3306', 'db2:3306']

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['alertmanager:9093']
```

#### **Alert Rules Example:**
```yaml
# Alert Rules (alert_rules.yml)
groups:
- name: availability_alerts
  rules:
  - alert: HighErrorRate
    expr: rate(http_errors_total[5m]) > 0.05
    for: 5m
    annotations:
      summary: "High error rate detected"
      description: "Error rate is {{ $value }} errors per second"
      
  - alert: DatabaseDown
    expr: up{job="databases"} == 0
    for: 1m
    annotations:
      summary: "Database is down"
      description: "Database {{ $labels.instance }} is not responding"
      
  - alert: HighResponseTime
    expr: http_request_duration_seconds{quantile="0.95"} > 0.5
    for: 2m
    annotations:
      summary: "High response time"  
      description: "95th percentile response time is {{ $value }}s"
```

---

### 5. 🌍 Geographic Distribution

**Purpose:** Survive regional disasters and reduce latency

#### **Geographic Strategies:**

| Strategy | Scope | Availability Benefit |
|----------|--------|-------------------|
| **Multi-AZ** | Multiple zones in same region | Survive data center outages |
| **Multi-Region** | Multiple geographic regions | Survive regional disasters |
| **Edge Computing** | Compute close to users | Reduce latency and load |
| **CDN** | Content cached globally | Faster content delivery |

#### **Multi-Region Architecture Example:**
```
Global E-commerce Platform:

Primary Regions:
├─ US-East (Virginia) - Primary for Americas
├─ EU-West (Ireland) - Primary for Europe  
└─ Asia-Pacific (Singapore) - Primary for Asia

Each Region Contains:
├─ 3 Availability Zones
├─ Load balancers in each AZ
├─ Application servers (auto-scaling)
├─ Database cluster (master + replicas)
├─ Redis cache cluster
└─ CDN edge locations

Disaster Recovery:
- Regional failure: Traffic routes to nearest healthy region
- Cross-region database replication (RPO < 5 minutes)
- Automated DNS failover (RTO < 2 minutes)
- Data backup in multiple regions
```

#### **CDN Configuration Example:**
```javascript
// CloudFlare CDN Configuration
const cdnConfig = {
  // Cache static content for 1 year
  cache_level: 'aggressive',
  browser_ttl: 31536000, // 1 year
  
  // Health checks for origin servers
  health_checks: {
    enabled: true,
    interval: 60, // seconds
    retries: 3,
    timeout: 10
  },
  
  // Geographic routing
  load_balancing: {
    enabled: true,
    pools: [
      {
        name: 'us-east',
        origins: ['origin1.company.com', 'origin2.company.com'],
        region: 'nam' // North America
      },
      {
        name: 'eu-west', 
        origins: ['origin3.company.com', 'origin4.company.com'],
        region: 'eur' // Europe
      }
    ]
  }
};
```

---

## ⚖️ Consistency Models

### 🎯 **CAP Theorem Trade-offs:**

**You can only guarantee 2 out of 3:**
- **Consistency** - All nodes see the same data
- **Availability** - System remains operational  
- **Partition Tolerance** - System continues despite network failures

### 📊 **Consistency Models Comparison:**

| Model | Consistency Level | Availability Impact | Use Cases |
|-------|------------------|-------------------|-----------|
| **Strong Consistency** | All nodes same data always | Lower availability | Banking, Financial systems |
| **Weak Consistency** | No guarantees when nodes sync | Higher availability | Caching, Analytics |
| **Eventual Consistency** | Nodes sync eventually | High availability | Social media, Content systems |

### 💡 **Real Examples:**

#### **Strong Consistency - Banking System:**
```sql
-- Bank Transfer (ACID Transaction)
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE id = 'sender';
UPDATE accounts SET balance = balance + 100 WHERE id = 'receiver';

-- Only commits if both operations succeed
-- All database replicas must confirm before commit
-- High consistency, but may be unavailable during network issues

COMMIT;
```

#### **Eventual Consistency - Social Media:**
```
Facebook Post Scenario:
1. User posts status update
2. Post immediately visible to user (local write)
3. Post propagates to friends' feeds over next few seconds
4. Eventually all friends see the post

Trade-off:
✅ High availability (can always post)
⚠️ Temporary inconsistency (friends see post at different times)
✅ Eventually consistent (all friends eventually see post)
```

#### **Weak Consistency - Cache System:**
```python
# Cache Implementation
class WeaklyConsistentCache:
    def __init__(self):
        self.cache_nodes = ['cache1', 'cache2', 'cache3']
    
    def set(self, key, value):
        # Write to random cache node
        node = random.choice(self.cache_nodes)
        node.set(key, value)
        # Don't wait for replication to other nodes
        return "OK"
    
    def get(self, key):
        # Read from random cache node
        node = random.choice(self.cache_nodes)
        return node.get(key)  # Might be stale or missing

# Trade-off: Fast operations, but data might be inconsistent
```

---

## 🌍 Real-World Examples

### 🛒 **Amazon - 99.99% Availability:**

```
Architecture for High Availability:

Global Infrastructure:
├─ 25+ Geographic regions
├─ 80+ Availability zones  
├─ 200+ Edge locations
└─ Multiple data centers per AZ

Service Architecture:
├─ Microservices (1000+ services)
├─ Load balancers at every layer
├─ Database sharding and replication
├─ Multi-region backups
└─ Automated failover

Availability Strategies:
- Everything redundant (no single points of failure)
- Circuit breakers prevent cascade failures
- Graceful degradation (remove features vs crash)
- Chaos engineering (intentionally break things)

Results:
- 99.99% availability maintained
- Black Friday: 10x traffic handled smoothly
- Regional disasters: Automatic failover
- Revenue: $469 billion (enabled by availability)
```

### 🎬 **Netflix - 99.9% Global Availability:**

```
Streaming Platform Requirements:
- 200M+ subscribers globally
- 1 billion+ hours watched monthly
- < 100ms response time globally
- Minimal buffering during peak hours

High Availability Implementation:

Content Delivery:
├─ 15,000+ CDN servers globally
├─ Content pre-positioned based on popularity
├─ Multiple encoding formats per video
└─ Intelligent client failover

Application Layer:
├─ Microservices on AWS
├─ Auto-scaling based on demand
├─ Circuit breakers for service protection
└─ Chaos Monkey for resilience testing

Data Layer:  
├─ Cassandra clusters (multi-region)
├─ Eventually consistent user data
├─ Strong consistency for billing
└─ Real-time analytics pipeline

Results:
- 99.9%+ availability globally
- Survives AWS region failures  
- Handles Super Bowl traffic spikes
- Customer satisfaction: 90%+ rating
```

### 🏦 **Banking System - 99.999% Availability:**

```
Critical Requirements:
- Zero data loss tolerance
- Sub-second transaction processing
- 24/7/365 operation required
- Regulatory compliance mandatory

High Availability Design:

Data Centers:
├─ Primary data center (active)
├─ Secondary data center (hot standby)
├─ Disaster recovery site (cold backup)
└─ Real-time synchronous replication

Application Architecture:
├─ Mainframe systems (proven reliability)
├─ Redundant network connections
├─ Hardware load balancers (F5)
├─ Database clustering (Oracle RAC)
└─ Backup power systems (UPS + generators)

Monitoring and Operations:
├─ 24/7 Network Operations Center
├─ Real-time system monitoring
├─ Automated incident response
├─ Regular disaster recovery testing
└─ Compliance auditing

Results:
- 99.999% availability achieved
- RPO: 0 seconds (no data loss)
- RTO: < 30 seconds for failover
- Regulatory compliance maintained
```

### 🎮 **Gaming Platform - Variable Availability:**

```
Different Availability Requirements:

Game Servers (99.99%):
├─ Real-time multiplayer critical
├─ Regional server clusters
├─ Quick matchmaking required
└─ Low latency mandatory

User Profiles (99.9%):
├─ Load player data quickly
├─ Cross-platform synchronization
├─ Achievement tracking
└─ Friend lists and social features

Store/Payments (99.999%):
├─ Revenue-critical transactions
├─ Strong consistency required
├─ Fraud detection systems
└─ Payment processing reliability

Leaderboards (99%):
├─ Can tolerate brief outages
├─ Eventually consistent data
├─ Batch processing acceptable
└─ Non-critical for gameplay

Architecture Strategy:
- Different availability targets per component
- Higher investment in revenue-critical systems  
- Graceful degradation for non-essential features
- Cost optimization based on business impact
```

---

## 🎤 Interview-Ready Summary

### ❓ **"What is high availability and why is it important?"**

**Framework Answer:**
> "High availability is the ability of a system to remain operational and accessible to users over time, typically measured in uptime percentages like 99.9% or 99.99%. It's critical because downtime directly impacts revenue, customer trust, and competitive advantage. For example, Amazon loses $34M per hour during outages, making high availability a business necessity."

### ❓ **"How do you achieve high availability?"**

**Key Strategies:**
1. **Eliminate single points of failure** - Redundancy at every layer
2. **Implement automatic failover** - Quick recovery from failures
3. **Use load balancing** - Distribute traffic and detect failures
4. **Geographic distribution** - Survive regional disasters
5. **Monitor proactively** - Detect and resolve issues before users notice
6. **Plan for disasters** - Regular testing and documented procedures

### ❓ **"What's the difference between 99.9% and 99.99% availability?"**

**Impact Comparison:**
- **99.9%**: 8.77 hours downtime/year, ~$100K lost revenue for typical e-commerce
- **99.99%**: 52.6 minutes downtime/year, ~$10K lost revenue
- **Cost**: 10x more expensive to achieve 99.99% vs 99.9%
- **Implementation**: More redundancy, better monitoring, faster recovery

### ❓ **"How do you handle the trade-off between consistency and availability?"**

**Trade-off Examples:**
- **Banking**: Choose consistency over availability (strong consistency)
- **Social Media**: Choose availability over consistency (eventual consistency)
- **E-commerce**: Different models for different features (strong for payments, eventual for recommendations)
- **Caching**: Weak consistency for performance

### ❓ **"What causes system outages and how do you prevent them?"**

**Common Causes & Prevention:**
- **Hardware failures** → Redundancy and monitoring
- **Software bugs** → Testing, gradual rollouts, rollback procedures
- **Network issues** → Multiple network paths, geographic distribution
- **Human errors** → Automation, procedures, access controls
- **Capacity overload** → Auto-scaling, load testing, capacity planning
- **External dependencies** → Circuit breakers, timeouts, fallback systems

---

## 💡 Key Takeaways

### **🎯 Essential Points:**
- **Availability = Business Success** - Downtime directly impacts revenue and reputation
- **"Nines" matter** - Each additional "9" costs 10x more but provides significant business value  
- **No single points of failure** - Everything must be redundant
- **Proactive monitoring** - Detect and fix issues before users notice
- **Trade-offs exist** - Balance consistency, availability, and partition tolerance

### **🚀 Implementation Strategy:**
1. **Define availability targets** - Based on business requirements and budget
2. **Identify single points of failure** - Eliminate them systematically
3. **Implement monitoring** - Comprehensive observability stack
4. **Test failure scenarios** - Regular disaster recovery testing
5. **Continuous improvement** - Learn from incidents and optimize

### **🎯 For Interviews:**
- **Know the business impact** - Connect availability to revenue
- **Understand trade-offs** - CAP theorem and consistency models
- **Think practically** - Real implementation strategies
- **Consider costs** - Higher availability costs exponentially more

### **⚖️ Remember:**
Perfect availability (100%) is impossible and infinitely expensive. The goal is to achieve the **right level of availability** for your business needs while optimizing for cost and complexity.

---

## 📚 Source
[Design Gurus - Availability](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/availability)

## 🏷️ Tags
`#SystemDesign` `#Availability` `#HighAvailability` `#Reliability` `#Redundancy` `#Interview`

## 🔗 Quick Links
- [🔝 Back to Top](#-availability---system-design-fundamentals)
- [📋 Table of Contents](#-table-of-contents)

---

> **💡 Pro Tip:** In system design interviews, always discuss availability targets in business terms. Connect uptime percentages to actual downtime hours and potential revenue impact. Show that you understand availability is not just a technical metric, but a business requirement!