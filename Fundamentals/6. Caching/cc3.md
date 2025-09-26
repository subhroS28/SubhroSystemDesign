# 💾 Types of Caching - System Design Fundamentals

## 📋 Overview

**What is Caching?** Storing frequently accessed data in faster storage to reduce retrieval time.

### 🏎️ **Speed Hierarchy (Fastest to Slowest):**

```
CPU Cache       → 1 ns          (Lightning fast)
RAM (In-Memory) → 100 ns        ⚡ Like grabbing from your desk
SSD (Disk)      → 100,000 ns    📁 Like opening filing cabinet
Network         → 1,000,000 ns  🐌 Like going to library across town
```

### 🏢 **Caching Layers in E-commerce (Like Lowes):**

```
User Browser     → Client-side caching (product images, CSS)
    ↓
CDN Edge Servers → Global caching (static assets worldwide)
    ↓
Load Balancer    → DNS caching (domain resolution)
    ↓
Web Server       → Server-side caching (page fragments)
    ↓
App Server       → In-memory caching (user sessions, hot products)
    ↓
Database         → Database caching (query results)
    ↓
Disk Storage     → Disk caching (generated reports)
```

---

## 🧠 1. In-Memory Caching

### 🍕 **Pizza Counter Analogy:**
Think of popular pizza slices kept warm at the counter vs. making fresh pizza each time.

**What:** Stores data in RAM (Redis, Memcached)

### 📊 **How It Works:**
```
Customer orders "Margherita" Pizza:
Step 1: Check hot counter (In-Memory Cache)
    ├─→ Available: Serve immediately (5 seconds) ⚡
    └─→ Not available: Make fresh pizza (15 minutes) 🐌
Step 2: Keep slice on counter for next customer
```

**Perfect For:**
- User login sessions (stay logged in)
- Shopping cart contents
- Recently viewed products
- API responses for trending items

**Real Numbers:** 500x faster than database queries

---

## 💿 2. Disk Caching

### 🏪 **Warehouse Analogy:**
Like keeping popular items in local store vs. ordering from main warehouse every time.

**What:** Stores data on hard drives/SSDs

### 📦 **Warehouse Flow:**
```
Customer wants seasonal catalog:
Local Store (Disk Cache):
    ├─→ In stock: Grab from shelf (2 minutes)
    └─→ Out of stock: Order from warehouse (2 days)
         Then stock locally for future customers
```

**Perfect For:**
- Monthly sales reports (expensive to generate)
- Product catalogs
- Large video files
- Backup data

**Key Benefit:** Survives power outages, app restarts

---

## 🗄️ 3. Database Caching

### 📚 **Librarian's Desk Analogy:**
Librarian keeps frequently requested books on their desk instead of walking to shelves every time.

**What:** Cache within database system

### 📖 **Library Process:**
```
Student asks for "Popular Science Book":
Librarian's Desk (DB Cache):
    ├─→ Book on desk: Hand over immediately
    └─→ Not on desk: Walk to shelf, get book
         Keep copy on desk for next student
```

**Perfect For:**
- Product category lists
- User profile information  
- Configuration settings
- Reference data (countries, currencies)

---

## 🌐 4. Client-Side Caching

### 🏠 **Home Pantry Analogy:**
Keeping groceries at home vs. going to store every time you need something.

**What:** Caching on user's device (browser, mobile app)

### 🛒 **Grocery Shopping:**
```
Want to cook pasta:
Home Pantry (Browser Cache):
    ├─→ Have ingredients: Cook immediately ⚡
    └─→ Missing items: Go to grocery store 🚗
         Stock pantry for future meals
```

**Perfect For:**
- Website logos, icons
- CSS stylesheets
- JavaScript files
- Previously viewed product images

**User Benefits:** Pages load instantly, works offline

---

## 🖥️ 5. Server-Side Caching

### 🍳 **Restaurant Kitchen Analogy:**
Pre-preparing popular dishes vs. cooking everything from scratch per order.

**What:** Caching on web server

### 👨‍🍳 **Kitchen Preparation:**
```
Customer orders "Today's Special":
Kitchen Prep Station (Server Cache):
    ├─→ Pre-made portions: Serve in 2 minutes ⚡
    └─→ Not prepared: Cook from scratch (20 minutes)
         Prepare extra portions for rush hour
```

**Perfect For:**
- Homepage content
- Product category pages
- Search result pages
- User dashboards

---

## 🌍 6. CDN Caching

### 🏪 **Franchise Store Analogy:**
McDonald's has outlets everywhere vs. one restaurant serving the whole world.

**What:** Global network of servers caching content

### 🍔 **Global Fast Food:**
```
Person in Mumbai wants Big Mac:
    ├─→ Local McDonald's: Get burger in 5 minutes ⚡
    └─→ Fly to USA McDonald's: 20 hours + jet lag 🐌

CDN = Local McDonald's in every city!
```

**Perfect For:**
- Product images worldwide
- Videos (Netflix, YouTube)
- Software downloads
- Mobile app assets

**Impact:** 10x faster for global users

---

## 🌐 7. DNS Caching

### 📞 **Phone Book Analogy:**
Keeping frequently called numbers in your phone vs. looking up phone book every time.

**What:** Caches domain name → IP address mappings

### 📱 **Making a Call:**
```
Want to call "John's Pizza":
Your Phone Contacts (DNS Cache):
    ├─→ Number saved: Direct dial ⚡
    └─→ Not saved: Look up phone book 📖
         Save number for future calls
```

**DNS Resolution Process:**
```
Type "google.com" in browser:
    ↓
Check DNS Cache (1ms) ⚡
    ├─→ Found: Connect to website
    └─→ Not found: Ask DNS servers (100ms)
         Cache result for next visit
```

---

## ⚖️ **Caching Types Comparison**

| Type | Speed | Like | Best For | Example |
|------|-------|------|----------|---------|
| **In-Memory** | ⚡⚡⚡⚡⚡ | Pizza counter | Hot data | User sessions |
| **Disk** | ⚡⚡⚡ | Local warehouse | Large files | Reports |
| **Database** | ⚡⚡⚡ | Librarian's desk | Query results | Categories |
| **Client-Side** | ⚡⚡⚡⚡ | Home pantry | Static files | CSS, images |
| **Server-Side** | ⚡⚡⚡ | Kitchen prep | Pages | Homepage |
| **CDN** | ⚡⚡⚡⚡ | Franchise stores | Global content | Videos |
| **DNS** | ⚡⚡⚡⚡⚡ | Phone contacts | Domain lookup | Website access |

## 🎯 **Decision Tree - When to Use What?**

```
What type of data?
├─→ User-specific (sessions, cart)
│   └─→ Use: In-Memory Cache
│
├─→ Static files (images, CSS)
│   ├─→ Global users? → Use: CDN Cache
│   └─→ Local users? → Use: Client-Side Cache
│
├─→ Large generated files (reports)
│   └─→ Use: Disk Cache
│
├─→ Database results
│   └─→ Use: Database Cache
│
├─→ Rendered pages
│   └─→ Use: Server-Side Cache
│
└─→ Domain resolution
    └─→ Use: DNS Cache (automatic)
```

## 🛒 **Real E-commerce Example:**

```
Amazon Product Page Loading:

1. DNS Cache: amazon.com → IP address (1ms)
2. CDN Cache: Product images from nearest edge (20ms)
3. Client Cache: CSS, JS files from browser (0ms - instant!)
4. Server Cache: Product page template (5ms)
5. In-Memory: User cart, recently viewed (0.1ms)
6. Database Cache: Product details, reviews (10ms)
7. Disk Cache: Related product recommendations (50ms)

Total: Page loads in ~86ms instead of 2+ seconds!
```

## 🎤 **Interview-Ready Answer**

**"Explain different types of caching":**

> "Think of caching like having multiple storage levels - like keeping snacks in your pocket (in-memory), pantry at home (client-side), local store (disk), and global franchise (CDN). Each level serves different needs:
>
> • **In-Memory** (Redis) - Hot data like user sessions - pizza counter analogy
> • **Client-Side** (Browser) - Static assets - home pantry analogy  
> • **CDN** - Global content distribution - McDonald's franchise analogy
> • **Database** - Query results - librarian's desk analogy
> • **Server-Side** - Rendered pages - kitchen prep analogy
> • **Disk** - Large persistent data - local warehouse analogy
> • **DNS** - Domain resolution - phone contacts analogy
>
> Most systems use multiple types in layers for optimal performance."

**E-commerce Strategy:**
```
Flipkart Caching Layers:
├── Product images → CDN (global users)
├── User sessions → In-Memory (fast access)
├── Search results → Server-Side (computed pages)
├── Category data → Database (shared queries)
├── CSS/JS → Client-Side (static assets)
└── Sales reports → Disk (large, persistent)
```

## 📊 **Key Performance Numbers**

```
Speed Improvements:
├── In-Memory: 500-1000x faster than database
├── CDN: 10-15x faster for global users
├── Client-Side: Eliminates network completely
├── DNS: 100x faster domain resolution
└── Database: 20-50x faster than full query

Cache Hit Ratios (Good targets):
├── In-Memory: >90%
├── CDN: >85%
├── Database: >80%
└── Client-Side: >95%
```

---

## 📚 Source
[Design Gurus - Types of Caching](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/types-of-caching)

## 🏷️ Tags
`#SystemDesign` `#Caching` `#Performance` `#Interview`