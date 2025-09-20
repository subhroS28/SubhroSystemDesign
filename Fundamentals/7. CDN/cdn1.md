# 🌐 Content Delivery Network (CDN) - System Design Fundamentals

## 📋 Table of Contents
- [Core Definition](#-core-definition)
- [How CDN Works](#-how-cdn-works)
- [Key Terminology](#-key-terminology)
- [Benefits](#-benefits)
- [Real-World Examples](#-real-world-examples)
- [Interview-Ready Summary](#-interview-ready-summary)

---

## 🎯 Core Definition

**What is CDN?**
A Content Delivery Network is a **distributed network of servers** strategically placed across the globe to deliver web content faster to users.

### 🍕 Think of it like Pizza Delivery Chains:
- **Without CDN**: One central kitchen serving entire country (slow, far deliveries)
- **With CDN**: Multiple kitchens in every city (fast, nearby deliveries)

### 📦 What CDNs Deliver:
- **Images** - Product photos, thumbnails, icons
- **Videos** - YouTube videos, Netflix streams
- **Static Files** - CSS, JavaScript, fonts
- **Documents** - PDFs, downloads
- **API Responses** - Cached API data

---

## 🔄 How CDN Works

### 📍 **The Journey of a Request**

```
User in Mumbai wants image.jpg
    ↓
1. Request goes to nearest CDN server (Mumbai PoP)
    ↓
2. CDN checks: "Do I have this image?"
    ↓
    ├─→ YES: Serve from cache (FAST! ~50ms)
    │
    └─→ NO: Fetch from origin server
           Cache it locally
           Serve to user (~500ms first time, then fast)
    ↓
3. Next user in Mumbai gets it in ~50ms!
```

### ⚡ **Step-by-Step Example**

**Scenario:** You're watching a YouTube video in Bangalore

```
Step 1: Click on video
Step 2: Request routed to Bangalore CDN server (Edge Server)
Step 3: Edge server checks its cache
Step 4a: If video cached → Stream immediately (50ms)
Step 4b: If not cached → Fetch from YouTube's origin server (500ms)
Step 5: Cache video for next viewers
Result: Next person in Bangalore gets instant playback!
```

---

## 🗝️ Key Terminology

### 1. **Point of Presence (PoP)** 🏢
**What:** Physical data center location with CDN servers

**Example:**
- Amazon CloudFront has PoPs in: Mumbai, Delhi, Bangalore, Chennai
- When you access amazon.in, you hit the nearest PoP
- Closer PoP = Faster loading

**Real Impact:** 
- User in Delhi: 20ms latency
- User in Bangalore: 30ms latency
- Both fast because of nearby PoPs!

### 2. **Edge Server** 🖥️
**What:** Actual server at a PoP that caches content

**Real Example:**
```
Netflix has edge servers in:
├─ Mumbai PoP: 50 edge servers
├─ Delhi PoP: 30 edge servers  
└─ Bangalore PoP: 40 edge servers

Each caches popular shows for local viewers
```

### 3. **Origin Server** 🏭
**What:** Your main server where original content lives

**Example:**
- Your Spring Boot app running on AWS Mumbai
- This is where you upload new product images
- CDN fetches from here and distributes globally

### 4. **Cache Warming** 🔥
**What:** Pre-loading content before users request it

**Use Case:**
```
New iPhone launched at 11 AM
Apple pre-loads product images at 10:30 AM to all CDN servers
At 11 AM launch: Images load instantly for everyone!
```

### 5. **Time to Live (TTL)** ⏰
**What:** How long content stays cached before checking for updates

**Common Settings:**
- **Images:** 7-30 days (rarely change)
- **JavaScript/CSS:** 1 hour - 1 day (can update frequently)
- **HTML:** 5-15 minutes (dynamic content)
- **Videos:** 30 days (large, stable)

**Example:**
```java
// In your Spring Boot app
@GetMapping("/product-image")
public ResponseEntity<byte[]> getProductImage() {
    return ResponseEntity.ok()
        .cacheControl(CacheControl.maxAge(7, TimeUnit.DAYS))
        .body(imageBytes);
}
```

### 6. **Anycast** 📡
**What:** Smart routing that sends user to nearest server automatically

**How it Works:**
```
Single IP address: 13.35.1.100
User in Mumbai → Routed to Mumbai PoP
User in USA → Routed to Virginia PoP
Same IP, different physical servers!
```

### 7. **Content Invalidation** 🔄
**What:** Updating cached content when origin changes

**Real Scenario:**
```
You fix a bug in product image
Step 1: Upload new image to origin server
Step 2: Invalidate CDN cache
Step 3: CDN fetches fresh image
Step 4: Users see updated image
```

### 8. **Cache Purging** 🗑️
**What:** Force remove content from cache

**When to Use:**
- **Immediate updates:** Security fix, wrong price displayed
- **Scheduled cleanup:** Remove old seasonal content
- **Legal reasons:** Copyright takedown requests

---

## ✨ Benefits

### 1. **⚡ Reduced Latency**

**Without CDN:**
```
User in Australia → Origin Server in India → 300ms latency
```

**With CDN:**
```
User in Australia → Sydney CDN → 20ms latency
15x FASTER!
```

### 2. **🚀 Improved Performance**

**Real Numbers:**
- **Without CDN:** 
  - Homepage loads in 3.5 seconds
  - Origin server handling 10,000 requests/sec
  
- **With CDN:**
  - Homepage loads in 0.8 seconds
  - Origin server handling only 500 requests/sec
  - 90% traffic handled by CDN!

### 3. **🛡️ Enhanced Reliability**

**Failure Scenario:**
```
Mumbai PoP goes down
    ↓
Anycast automatically routes Mumbai users
    ↓
To Delhi or Bangalore PoP
    ↓
Users experience slight delay, but no downtime!
```

### 4. **📈 Scalability**

**Black Friday Traffic Spike:**
```
Normal Day: 10,000 users/hour
Black Friday: 500,000 users/hour

Without CDN: Origin server crashes 💥
With CDN: CDN handles spike, origin stays stable ✅
```

### 5. **🔒 Security**

**DDoS Protection:**
```
Attack traffic hits CDN edge servers first
CDN absorbs the attack
Origin server protected
Your Spring Boot app stays safe!
```

**Additional Features:**
- **WAF (Web Application Firewall)** - Blocks SQL injection, XSS attacks
- **SSL/TLS at Edge** - Encrypted communication closer to user
- **Bot Detection** - Filter out malicious bots

---

## 🌍 Real-World Examples

### **Netflix** 🎬
```
Problem: Stream 4K videos to millions globally
Solution: 
├─ 10,000+ edge servers worldwide
├─ Popular shows pre-cached in each region
└─ 95% traffic served from cache

Result: Smooth streaming, minimal buffering
```

### **Amazon** 🛒
```
Product Page Load:
├─ Product images: CDN cached (20ms)
├─ Product details: Dynamic, from origin (100ms)
└─ User reviews: Dynamic, from origin (100ms)

Result: Page appears instant, data loads progressively
```

### **Your E-commerce App** 🏪
```java
// Spring Boot Implementation
@Configuration
public class CDNConfig {
    
    @Bean
    public CacheControl imageCache() {
        // Product images cached for 30 days
        return CacheControl.maxAge(30, TimeUnit.DAYS)
                          .cachePublic();
    }
    
    @Bean
    public CacheControl apiCache() {
        // API responses cached for 5 minutes
        return CacheControl.maxAge(5, TimeUnit.MINUTES)
                          .cachePublic();
    }
}
```

---

## 🎤 Interview-Ready Summary

### **"Explain CDN in simple terms"**

**Answer:**
> "CDN is like having multiple copies of a library in different cities. Instead of everyone traveling to one central library in Delhi, each city has its own copy. When you want a book (web content), you go to your local library (nearest CDN server) - much faster than traveling to Delhi every time!"

### **"When would you use a CDN?"**

**Decision Framework:**
```
Use CDN when:
✅ Serving global users
✅ Heavy static content (images, videos, CSS, JS)
✅ High traffic volume
✅ Need better performance
✅ Want to reduce origin server load

Skip CDN when:
❌ Only local users (single city/country)
❌ Mostly dynamic, personalized content
❌ Very low traffic
❌ Content changes every second
```

### **"How does CDN improve your Spring Boot app?"**

**Real Implementation:**
```java
// Before CDN
@GetMapping("/api/products")
public List<Product> getProducts() {
    // Origin server handles ALL requests
    // Slow for distant users
    // Heavy load on server
    return productService.getAllProducts();
}

// After CDN
@GetMapping("/api/products")
public ResponseEntity<List<Product>> getProducts() {
    List<Product> products = productService.getAllProducts();
    
    return ResponseEntity.ok()
        .cacheControl(CacheControl.maxAge(5, TimeUnit.MINUTES))
        .body(products);
    // CDN caches for 5 minutes
    // Subsequent requests served from edge
    // 90% less load on origin
}
```

### **"What are CDN challenges?"**

| Challenge | Impact | Solution |
|-----------|--------|----------|
| **Stale Content** | Users see outdated data | Proper TTL + invalidation strategy |
| **Cache Poisoning** | Malicious content cached | Strong validation + purging |
| **Cost** | Can be expensive for high traffic | Smart caching + compression |
| **Complexity** | More moving parts | Good monitoring + documentation |

---

## 💡 Key Takeaways for FAANG Interviews

### **What Interviewers Want to Hear:**

1. **Understand Trade-offs:**
   - CDN great for static content
   - Not ideal for highly personalized data
   
2. **Know Real Numbers:**
   - "CDN can reduce latency from 500ms to 50ms"
   - "Can handle 90% of traffic, reducing origin load"

3. **Implementation Awareness:**
   - How to set cache headers
   - When to invalidate cache
   - Cost considerations

4. **Real-World Context:**
   - "For our Lowes app, we'd use CDN for product images..."
   - "Dynamic pricing would still hit origin..."

### **Practice Question:**
> **"Design a video streaming platform like Netflix"**

**CDN Answer:**
```
1. Use CDN for video chunks (large, static)
2. TTL: 30 days (videos rarely change)
3. Cache warming: Pre-load popular shows
4. Origin server: Only for user data, recommendations
5. Result: 95% requests served from edge servers
```

---

## 📚 Source
[Design Gurus - What is CDN?](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/what-is-cdn)

## 🏷️ Tags
`#SystemDesign` `#CDN` `#Performance` `#Caching` `#Interview` `#Scalability`

---

> **💡 Pro Tip for FAANG:** Always mention specific numbers (latency improvements, cache hit rates) and real implementation examples from your Spring Boot experience. This shows practical knowledge beyond theory!