# ⚖️ Load Balancing Algorithms - System Design Fundamentals

## 📋 Table of Contents
- [What Are Load Balancing Algorithms?](#-what-are-load-balancing-algorithms)
- [Algorithm Overview](#-algorithm-overview-quick-reference)
- [Detailed Algorithm Analysis](#-detailed-algorithm-analysis)
- [Algorithm Comparison](#-algorithm-comparison)
- [Interview-Ready Summary](#-interview-ready-summary)
- [Real-World Examples](#-real-world-examples)

---

## 🎯 What Are Load Balancing Algorithms?

Load balancing algorithms are **methods used by load balancers to distribute incoming traffic and requests among multiple servers**. Think of them as different strategies a restaurant host uses to seat customers across various dining sections.

### 🎯 **Primary Goals:**
- **Efficient resource utilization** - Make sure no server sits idle while others are overloaded
- **Improve system performance** - Optimize response times and throughput  
- **Maintain high availability** - Prevent any single server from becoming overwhelmed
- **Enhance user experience** - Ensure consistent, fast responses

---

## 📊 Algorithm Overview (Quick Reference)

| Algorithm | Best For | Complexity | Key Factor |
|-----------|----------|------------|------------|
| 🔄 **Round Robin** | Similar servers, stateless apps | Low | Fixed sequence |
| 🔗 **Least Connections** | Variable traffic, stateful apps | Medium | Active connections |
| ⚖️ **Weighted Round Robin** | Different server capacities | Medium | Server weights |
| 🎯 **Weighted Least Connections** | Mixed capacity + dynamic load | High | Weights + connections |
| 🏠 **IP Hash** | Session persistence needed | Low | Client IP address |
| ⚡ **Least Response Time** | Low latency requirements | High | Server response speed |
| 🎲 **Random** | Simple deployments | Very Low | Random selection |
| 📊 **Least Bandwidth** | High bandwidth apps | High | Network utilization |
| 🔧 **Custom Load** | Specific requirements | Very High | Custom metrics |

---

## 🔍 Detailed Algorithm Analysis

### 1. 🔄 Round Robin

**How it works:** Distributes requests in a cyclic order (1→2→3→1→2→3...)

| Pros ✅ | Cons ❌ |
|---------|---------|
| Equal distribution among servers | No load awareness |
| Easy to implement | No session affinity |
| Works well with similar capacities | Poor with different server capacities |
| Predictable pattern | Potentially exploitable by attackers |

**🎯 Perfect For:**
- **Homogeneous environments** - All servers have similar specs
- **Stateless applications** - Each request is independent (like REST APIs)

**💡 Real Example:** Netflix routing movie catalog requests across identical web servers

---

### 2. 🔗 Least Connections

**How it works:** Routes new requests to the server with fewest active connections

| Pros ✅ | Cons ❌ |
|---------|---------|
| Load-aware distribution | Higher implementation complexity |
| Adapts to traffic patterns | Requires state maintenance |
| Great for heterogeneous environments | Connection spike issues |

**🎯 Perfect For:**
- **Variable traffic patterns** - Unpredictable request volumes
- **Stateful applications** - Where sessions matter
- **Mixed server capacities** - Different server specs

**💡 Real Example:** E-commerce site during flash sales where request processing times vary greatly

---

### 3. ⚖️ Weighted Round Robin

**How it works:** Round robin with weights based on server capacity (powerful servers get more requests)

| Server | Weight | Requests Per Cycle |
|--------|--------|--------------------|
| Server A (High-end) | 3 | Gets 3 requests |
| Server B (Medium) | 2 | Gets 2 requests |
| Server C (Basic) | 1 | Gets 1 request |

| Pros ✅ | Cons ❌ |
|---------|---------|
| Capacity-based distribution | Complex weight assignment |
| Flexible and adjustable | Additional management overhead |
| Optimizes performance | Poor with variable loads |

**🎯 Perfect For:**
- **Heterogeneous server environments** - Mix of powerful and basic servers
- **Database clusters** - Some nodes are more powerful

---

### 4. 🎯 Weighted Least Connections

**How it works:** Combines weights (server capacity) + active connections for smart routing

**Formula:** `Score = Active Connections / Server Weight` → Route to lowest score

| Pros ✅ | Cons ❌ |
|---------|---------|
| Dynamic + capacity aware | Most complex implementation |
| Handles mixed environments | High state maintenance |
| Flexible adaptation | Challenging weight assignment |

**🎯 Perfect For:**
- **High-traffic web applications** - Need both capacity awareness and dynamic balancing
- **Mixed database clusters** - Different node capabilities with varying query loads

---

### 5. 🏠 IP Hash

**How it works:** Uses client IP address hash to determine server assignment

**Example:** IP `192.168.1.10` → Hash value `2` → Server C (2 % 3 = 2)

| Pros ✅ | Cons ❌ |
|---------|---------|
| Session persistence guaranteed | Uneven distribution possible |
| Simple implementation | Limited flexibility |
| Deterministic routing | Issues when adding/removing servers |

**🎯 Perfect For:**
- **Stateful applications** - Shopping carts, user sessions
- **Geographic distribution** - Consistent regional routing

**💡 Real Example:** Online banking where user sessions must stay on same server for security

---

### 6. ⚡ Least Response Time

**How it works:** Routes to server with fastest recent response times

**Process:**
1. Monitor response times continuously
2. Calculate average response time per server
3. Route new requests to fastest server
4. Dynamically adjust based on real-time data

| Pros ✅ | Cons ❌ |
|---------|---------|
| Optimized client experience | Complex implementation |
| Dynamic performance adjustment | Monitoring overhead |
| Effective resource utilization | Short-term variability issues |

**🎯 Perfect For:**
- **Real-time applications** - Gaming, video streaming, trading platforms
- **APIs requiring quick responses** - Financial services, real-time analytics

---

### 7. 🎲 Random

**How it works:** Randomly selects a server for each request

| Pros ✅ | Cons ❌ |
|---------|---------|
| Extremely simple | No load awareness |
| No state maintenance | Potential short-term imbalance |
| Uniform distribution over time | No session affinity |

**🎯 Perfect For:**
- **Simple deployments** - Basic web applications
- **Homogeneous environments** - All servers identical
- **Stateless applications** - Each request independent

---

### 8. 📊 Least Bandwidth

**How it works:** Routes to server consuming least network bandwidth

| Pros ✅ | Cons ❌ |
|---------|---------|
| Prevents bandwidth overload | Complex monitoring required |
| Dynamic network balancing | Additional overhead |
| Efficient resource utilization | Short-term fluctuations |

**🎯 Perfect For:**
- **Video streaming services** - High bandwidth applications
- **Content Delivery Networks (CDNs)** - Large file transfers
- **File download services** - Bandwidth-intensive operations

---

### 9. 🔧 Custom Load

**How it works:** Define your own metrics and rules for load balancing

**Custom Metrics Examples:**
- CPU usage + Memory usage + Disk I/O
- Application-specific metrics
- Combined performance indicators

| Pros ✅ | Cons ❌ |
|---------|---------|
| Highly flexible and customizable | Most complex to implement |
| Optimized for specific needs | High monitoring overhead |
| Adaptable to changing requirements | Risk of misconfiguration |

**🎯 Perfect For:**
- **Complex applications** - Multiple performance factors
- **Dynamic environments** - Rapidly changing workloads
- **Specialized requirements** - When standard algorithms don't fit

---

## ⚔️ Algorithm Comparison

### 📈 **Complexity vs Performance**

| Algorithm | Implementation | Performance | Use Case |
|-----------|----------------|-------------|----------|
| Random | ⭐ Very Easy | ⭐⭐ Basic | Simple apps |
| Round Robin | ⭐⭐ Easy | ⭐⭐⭐ Good | Uniform servers |
| Least Connections | ⭐⭐⭐ Medium | ⭐⭐⭐⭐ Great | Variable traffic |
| Weighted Round Robin | ⭐⭐⭐ Medium | ⭐⭐⭐⭐ Great | Mixed capacities |
| IP Hash | ⭐⭐ Easy | ⭐⭐⭐ Good | Sessions needed |
| Least Response Time | ⭐⭐⭐⭐ Hard | ⭐⭐⭐⭐⭐ Excellent | Low latency |
| Weighted Least Connections | ⭐⭐⭐⭐⭐ Very Hard | ⭐⭐⭐⭐⭐ Excellent | Complex environments |

---

## 🎤 Interview-Ready Summary

### ❓ **When asked "Which algorithm would you choose?"**

| Scenario | Algorithm | Reason |
|----------|-----------|---------|
| **Identical servers, stateless app** | Round Robin | Simple, equal distribution |
| **Different server capacities** | Weighted Round Robin | Capacity-aware distribution |
| **Unpredictable traffic** | Least Connections | Adapts to real-time load |
| **Need session persistence** | IP Hash | Same client → same server |
| **Low latency critical** | Least Response Time | Routes to fastest server |
| **Mixed capacity + dynamic load** | Weighted Least Connections | Best of both worlds |

### ❓ **When asked "What factors influence algorithm choice?"**
- **Server homogeneity** - Similar vs different specs
- **Application state** - Stateless vs stateful
- **Traffic patterns** - Predictable vs variable
- **Performance requirements** - Basic vs high-performance
- **Implementation complexity** - Simple vs sophisticated

---

## 🌍 Real-World Examples

### **🎬 Netflix:**
- **Catalog browsing:** Round Robin (stateless, similar servers)
- **Video streaming:** Least Bandwidth (high bandwidth usage)
- **User sessions:** IP Hash (maintain viewing history)

### **🛒 Amazon:**
- **Product search:** Weighted Round Robin (mixed server capacities)
- **Shopping cart:** IP Hash (session persistence)
- **Checkout:** Least Response Time (fast transaction processing)

### **🎮 Gaming Platform:**
- **Game matchmaking:** Least Response Time (low latency critical)
- **User profiles:** Least Connections (dynamic traffic)
- **Static content:** Round Robin (simple distribution)

---

## 💡 Key Takeaway

**Choose algorithms based on your specific needs:**
- **Start simple** (Round Robin) and evolve as requirements grow
- **Consider your servers** - Are they identical or mixed capacity?
- **Think about your app** - Stateless or need sessions?
- **Performance requirements** - Basic distribution or optimized routing?

> **🎯 Pro Tip:** Most real-world systems use **multiple algorithms at different layers** - Round Robin for web servers, Least Connections for app servers, IP Hash for database connections!

---

## 📚 Source
[Design Gurus - Load Balancing Algorithms](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/load-balancing-algorithms)

## 🏷️ Tags
`#SystemDesign` `#LoadBalancing` `#Algorithms` `#Interview` `#Backend` `#Performance`

## 🔗 Quick Links
- [🔝 Back to Top](#️-load-balancing-algorithms---system-design-fundamentals)
- [📋 Table of Contents](#-table-of-contents)

---

> **💡 Interview Tip:** Be ready to explain trade-offs! Every algorithm has pros/cons - knowing when to use which one shows deep understanding!