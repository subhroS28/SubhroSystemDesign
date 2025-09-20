# 🏗️ CDN Architecture - System Design Fundamentals

## 📋 Table of Contents
- [Points of Presence (PoPs) and Edge Servers](#-points-of-presence-pops-and-edge-servers)
- [CDN Routing and Request Handling](#-cdn-routing-and-request-handling)
- [Caching Mechanisms](#-caching-mechanisms)
- [CDN Network Topologies](#-cdn-network-topologies)
- [Interview-Ready Summary](#-interview-ready-summary)

---

## 🗺️ Points of Presence (PoPs) and Edge Servers

### **What is a Point of Presence (PoP)?**

A **physical location** containing a group of edge servers within the CDN's distributed network.

### 🎯 **Key Characteristics:**

**Strategic Placement:**
- Located across various geographical regions
- Purpose: Minimize latency for users
- Each PoP contains multiple edge servers

**Why Multiple Edge Servers per PoP?**
```
Benefits of Multiple Servers:
├── Redundancy: If one fails, others continue
├── Fault Tolerance: Automatic failover
└── Load Balancing: Distribute user requests evenly
```

### 📍 **Real Example:**

```
CloudFront PoP in Mumbai:
├── Location: Mumbai Data Center
├── Contains: 20 edge servers
├── Serves: Western India users
├── Benefit: Reduces latency from 300ms → 20ms
└── Redundancy: If 1 server fails, 19 others available
```

### 💡 **How Edge Servers Work:**

**Edge Server Role:**
1. **Store cached content** from origin server
2. **Serve content** directly to users
3. **Fetch new content** when not in cache

**Request Flow:**
```
User Request → Nearest Edge Server
                    ↓
              Check Cache?
                ├─→ YES: Serve from cache (Fast!)
                └─→ NO: Fetch from origin → Cache → Serve
```

**Key Benefit:** 
> By serving content from geographically nearest server, CDNs significantly reduce latency and improve user experience.

---

## 🧭 CDN Routing and Request Handling

### **What is CDN Routing?**

The process of **directing user requests** to the most suitable edge server.

### 🎯 **Routing Decision Factors:**

```
Routing Based On:
├── Network latency (ping time)
├── Server load (current traffic)
└── User's geographical location
```

### **1️⃣ Anycast Routing**

**How it Works:**
- Multiple edge servers share **single IP address**
- Network automatically routes to nearest server
- Based on network latency or hop count

**Example:**
```
IP Address: 13.35.1.100

User in Mumbai → Routes to Mumbai edge server
User in Delhi → Routes to Delhi edge server
User in USA → Routes to Virginia edge server

All using same IP: 13.35.1.100!
```

**Key Benefit:** 
> Automatic routing to most appropriate server - no manual configuration needed.

**Spring Boot Usage:**
```java
// You just use one CDN URL
@Value("${cdn.url}")
private String cdnUrl; // https://d123456.cloudfront.net

// Anycast automatically routes users to nearest edge
public String getImageUrl(String image) {
    return cdnUrl + "/images/" + image;
}
```

### **2️⃣ DNS-based Routing**

**How it Works:**
- User requests content
- CDN's DNS server responds with **IP address of best edge server**
- Considers geographical proximity and server load

**Example:**
```
User in Mumbai asks: "Where is cdn.example.com?"
DNS Response: "52.95.123.45" (Mumbai edge server IP)

User in USA asks: "Where is cdn.example.com?"
DNS Response: "52.95.200.100" (Virginia edge server IP)
```

**Key Benefit:**
> Fine-grained control - can select best server based on multiple factors.

### **3️⃣ GeoIP-based Routing**

**How it Works:**
- User's location determined from IP address
- Request directed to nearest edge server geographically
- Lower geographical distance = lower network latency

**Example:**
```
User IP: 106.51.XX.XX
GeoIP Lookup: "Mumbai, India"
Route Decision: Send to Mumbai edge server
```

**Key Benefit:**
> Accurate location-based routing for optimal performance.

---

## 💾 Caching Mechanisms

### **Why Caching Matters:**

Caching is a **crucial component** of CDN architecture:
- ✅ Reduces latency
- ✅ Offloads traffic from origin server
- ✅ Improves overall performance

### **1️⃣ Time-to-Live (TTL)**

**What is TTL?**
- Value set by origin server
- Determines how long content stays in cache
- After TTL expires, content considered "stale"

**Example:**
```
Cache Lifetime Flow:

10:00 AM - Content cached with 1-hour TTL
10:30 AM - Request arrives → Serve from cache ✅
11:01 AM - TTL expired → Fetch fresh from origin
11:01 AM - New content cached for next hour
```

**Spring Boot Implementation:**
```java
@GetMapping("/products/{id}/image")
public ResponseEntity<byte[]> getImage(@PathVariable Long id) {
    return ResponseEntity.ok()
        .cacheControl(CacheControl.maxAge(30, TimeUnit.DAYS)) // TTL = 30 days
        .body(imageData);
}
```

**Common TTL Settings:**
```
Content Type        Typical TTL
────────────────────────────────
Static Images       30 days
CSS/JS Files        7 days
HTML Pages          5 minutes
API Responses       1-10 minutes
```

### **2️⃣ Cache Invalidation**

**What is Cache Invalidation?**
- Removing content from cache **before TTL expires**
- Needed when content updated or deleted on origin

**When to Use:**
```
Invalidate Cache When:
├── Product price changed
├── Image updated
├── Content deleted
└── Security fix deployed
```

**Implementation:**
```java
@Service
public class CacheInvalidationService {
    
    // Invalidate after product update
    public void invalidateProduct(Long productId) {
        String path = "/api/products/" + productId;
        
        // Call CDN API to remove from cache
        cdnClient.invalidate(path);
    }
}
```

**Key Benefit:**
> Ensures users immediately see updated content without waiting for TTL expiry.

### **3️⃣ Cache Control Headers**

**What are Cache Control Headers?**
- HTTP headers from origin server
- Provide instructions to CDN about caching behavior
- Control cacheability, TTL, and other settings

**Common Headers:**
```java
// Public - Can be cached by CDN and browsers
@GetMapping("/public/logo.png")
public ResponseEntity<byte[]> getLogo() {
    return ResponseEntity.ok()
        .header("Cache-Control", "public, max-age=31536000") // 1 year
        .body(logoData);
}

// Private - Only browser can cache, not CDN
@GetMapping("/user/profile")
public ResponseEntity<User> getProfile() {
    return ResponseEntity.ok()
        .header("Cache-Control", "private, max-age=3600")
        .body(userData);
}

// No-cache - Must revalidate
@GetMapping("/api/stock")
public ResponseEntity<Stock> getStock() {
    return ResponseEntity.ok()
        .header("Cache-Control", "no-cache")
        .body(stockData);
}
```

**Key Headers:**
```
Cache-Control: public, max-age=3600
├── public: CDN can cache
└── max-age=3600: Cache for 1 hour

Cache-Control: private, no-store
├── private: Only browser caches
└── no-store: Don't cache at all
```

---

## 🕸️ CDN Network Topologies

### **What is Network Topology?**

The **structure and organization** of CDN's distributed network. Different topologies optimize for performance, reliability, and cost.

### **1️⃣ Flat Topology**

**Structure:**
- All edge servers directly connected to origin server
- Simple architecture

```
        Origin Server
              |
    ┌────┬────┼────┬────┐
    ↓    ↓    ↓    ↓    ↓
  Edge Edge Edge Edge Edge
```

**Characteristics:**
```
✅ Simple and effective for smaller CDNs
✅ Easy to implement and manage

❌ Doesn't scale well
❌ High load on origin server
```

**Use Case:** Small startups with limited edge servers

### **2️⃣ Hierarchical Topology**

**Structure:**
- Edge servers organized into multiple tiers
- Each tier serves content to tier below
- Tree-like structure

```
            Origin Server
                  |
          Regional Hubs (Tier 1)
            |         |
        City PoPs (Tier 2)
          |   |   |   |
    Edge Servers (Tier 3)
```

**Characteristics:**
```
✅ Improves scalability
✅ Distributes load among multiple levels
✅ Reduces direct connections to origin

❌ More complex to manage
❌ Higher latency (multiple hops)
```

**Real Example:**
```
Akamai Hierarchy:
Origin (Mumbai) → Regional Hub (Asia) → City PoPs → Edge Servers

Benefits:
- 90% requests handled by lower tiers
- Origin server only handles 10% traffic
```

### **3️⃣ Mesh Topology**

**Structure:**
- Edge servers interconnected
- Can share content and load with each other
- Peer-to-peer communication

```
  Edge 1 ←→ Edge 2
    ↕  ✖     ↕
  Edge 3 ←→ Edge 4
```

**Characteristics:**
```
✅ Enhanced redundancy
✅ High fault tolerance
✅ Reduced origin server fetches

❌ Complex routing
❌ More bandwidth between edges
```

**Real Example:**
```
Netflix Mesh:
- Popular shows cached in multiple edges
- Edges share content with each other
- If Mumbai edge has content, Delhi edge fetches from Mumbai (faster!)
```

### **4️⃣ Hybrid Topology**

**Structure:**
- Combines elements from multiple topologies
- Optimized for specific needs
- Different strategies for different content types

```
Static Content → Hierarchical Topology
Dynamic Content → Flat Topology
Popular Content → Mesh Topology
```

**Real Implementation:**
```java
@Configuration
public class HybridTopologyConfig {
    
    public String getTopology(String contentType) {
        switch(contentType) {
            case "static":
                return "hierarchical"; // Images, CSS
            case "dynamic":
                return "flat"; // API responses
            case "video":
                return "mesh"; // Popular videos
            default:
                return "hierarchical";
        }
    }
}
```

**Example:**
```
E-commerce CDN:
├── Product Images: Hierarchical (scalable)
├── API Responses: Flat (low latency)
└── Popular Videos: Mesh (redundant)
```

---

## 🎯 Summary

**CDN Architecture involves:**

### **Four Key Components:**

**1. PoPs and Edge Servers**
- Strategic geographical placement
- Multiple servers for redundancy
- Minimize latency

**2. Routing Mechanisms**
- Anycast: Automatic routing
- DNS-based: Fine-grained control
- GeoIP: Location-based

**3. Caching Strategies**
- TTL: Time-based expiration
- Invalidation: Manual updates
- Cache Control Headers: Server instructions

**4. Network Topologies**
- Flat: Simple, small scale
- Hierarchical: Scalable, multi-tier
- Mesh: Redundant, fault-tolerant
- Hybrid: Best of all worlds

### **Benefits CDNs Provide:**

```
Performance Improvements:
├── Reduced latency (300ms → 20ms)
├── Faster page loads
├── Better user experience
└── Global reach

Reliability:
├── Fault tolerance
├── Redundancy
├── High availability
└── Automatic failover

Security:
├── DDoS protection
├── SSL/TLS termination
└── Web Application Firewall
```

---

## 🎤 Interview-Ready Summary

### **"Explain CDN Architecture in 2 minutes"**

**Perfect Answer:**
> "CDN architecture has four main components:
> 
> **1. PoPs and Edge Servers** - Geographically distributed data centers with multiple servers for redundancy.
> 
> **2. Routing** - Uses Anycast, DNS, or GeoIP to direct users to nearest edge server based on latency and location.
> 
> **3. Caching** - Edge servers cache content based on TTL, with invalidation for updates and cache control headers for fine-grained control.
> 
> **4. Topology** - Network structure can be flat (simple), hierarchical (scalable), mesh (redundant), or hybrid (optimized).
> 
> Together, these provide significant improvements in latency, performance, reliability, and security."

### **"Which routing method would you choose?"**

**Decision Framework:**
```
Choose Anycast when:
- Need automatic failover
- Want simplest client setup
- Performance is critical

Choose DNS-based when:
- Need granular control
- Want to consider server load
- Can tolerate DNS lookup delay

Choose GeoIP when:
- Need accurate location routing
- Want application-level control
- Have VPN/proxy considerations
```

### **"How do you decide topology?"**

**Structured Answer:**
```
Consider:
1. Scale: Hierarchical for large CDNs
2. Content type: Mesh for videos, Flat for APIs
3. Budget: Flat cheaper, Hierarchical more efficient
4. Reliability needs: Mesh for high availability

Most companies use Hybrid:
- Static content: Hierarchical
- Dynamic content: Flat
- Popular content: Mesh
```

### **Key Points to Remember:**

```
Numbers for Interviews:
├── PoPs reduce latency: 300ms → 20ms
├── Cache hit ratio target: >90%
├── TTL typical values: 5min - 30days
└── Topology levels: 2-4 tiers in hierarchical

Trade-offs:
├── Flat: Simple but doesn't scale
├── Hierarchical: Scalable but complex
├── Mesh: Redundant but expensive
└── Hybrid: Optimal but needs planning
```

---

## 📚 Source
[Design Gurus - CDN Architecture](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/cdn-architecture)

## 🏷️ Tags
`#SystemDesign` `#CDN` `#Architecture` `#PoP` `#Routing` `#Caching` `#Topology` `#Interview`

---

> **💡 Pro Tip:** In interviews, always mention the specific topology and routing you'd choose for the use case. Don't just explain concepts - apply them! "For Netflix, I'd use hierarchical topology for recorded content and mesh for live streams, with Anycast routing for automatic failover."