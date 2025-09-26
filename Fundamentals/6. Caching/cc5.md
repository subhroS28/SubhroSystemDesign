# 🔄 Cache Invalidation - System Design Fundamentals

## 📋 Overview

**Problem:** How to keep cached data fresh when original data changes?

### 🥛 **Milk Expiration Analogy:**
```
Scenario: You buy milk and store in fridge
├── Milk expires → Need to throw away old milk
├── Without checking → Drink spoiled milk 🤢
└── With checking → Fresh milk always ✅

Cache = Fridge storage
Invalidation = Checking expiration dates
```

### ⚠️ **The Stale Data Problem:**
```
Database: Product Price = $100 (NEW)
Cache:    Product Price = $80  (OLD/STALE)
User sees: $80 ❌ (Wrong price!)

Solution: Cache Invalidation strategies
```

---

## 🎯 Why Cache Invalidation Matters

### 📊 **The 4 Key Reasons:**

```
1. Data Freshness
   Database: iPhone price → $999
   Cache:    iPhone price → $1099 (old)
   Problem:  Customer sees wrong price

2. System Consistency  
   Layer 1 Cache: Product available
   Layer 2 Cache: Product out of stock
   Problem:      Conflicting information

3. Performance vs Accuracy Balance
   Too frequent refresh → Slow performance
   Too rare refresh    → Stale data
   Goal:               Sweet spot

4. Error Prevention
   Old inventory data → Wrong stock display
   Stale user data   → Security issues
   Goal:             Always accurate data
```

---

## ✍️ Cache Invalidation Strategies

### 🔄 **Write Pattern Comparison:**

```
User updates product price from $80 → $100

Strategy Flow:
Write-Through:  Cache ← Write → Database (Both updated)
Write-Around:   Cache ← Skip    Database (Only DB updated)  
Write-Back:     Cache ← Write   Database (Later)
Write-Behind:   Cache ← Write   Database (Scheduled)
```

## 📝 1. Write-Through Cache

### 🏦 **Bank Deposit Analogy:**
Like depositing money - teller updates both your receipt AND the bank's main ledger simultaneously.

**How It Works:**
```
User changes product price:
Step 1: Write to Cache ($100)     ✅
Step 2: Write to Database ($100)  ✅
Step 3: Confirm to user "Success!"

Both cache and database have same data instantly
```

### 📊 **Write-Through Flow:**
```
Application → Cache → Database
     ↑         ↓
     ←─────────┘
     "Success!"

Time: 50ms (both writes)
Consistency: Perfect ✅
Risk: None
```

**Perfect For:**
- Financial transactions
- Critical data updates  
- When consistency matters most
- User profile changes

**Pros:** ✅ Perfect consistency, no data loss
**Cons:** ❌ Slower writes (2x write operations)

## 🎯 2. Write-Around Cache

### 📫 **Post Office Analogy:**
Like sending important mail directly to recipient's home, bypassing local post office.

**How It Works:**
```
User changes product price:
Step 1: Skip Cache
Step 2: Write directly to Database ($100) ✅
Step 3: Cache still has old price ($80)

Next read: Cache miss → Fetch from DB → Update cache
```

### 📊 **Write-Around Flow:**
```
Application → Database (bypassing cache)
     ↑           ↓
     ←───────────┘
     "Success!"

Cache: Still has old data until next read
```

**Perfect For:**
- Write-heavy applications
- Data rarely read after writing
- Log entries, audit trails
- Bulk data imports

**Pros:** ✅ Fast writes, no cache pollution
**Cons:** ❌ Cache miss on recent writes, temporary inconsistency

## ⚡ 3. Write-Back Cache

### 📝 **Notebook Analogy:**
Like writing notes in your notebook during class, then copying to main notebook later when you have time.

**How It Works:**
```
User changes product price:
Step 1: Write to Cache ($100)      ✅
Step 2: Confirm to user "Success!" 
Step 3: Write to Database later... ⏰

Risk: If cache crashes before database write → Data lost!
```

### 📊 **Write-Back Flow:**
```
Application → Cache
     ↑         ↓ (immediate)
     ←─────────┘
     "Success!"
            
Cache → Database (later, when needed)
```

**Perfect For:**
- High-performance applications
- Gaming leaderboards
- Real-time analytics
- Temporary data

**Pros:** ✅ Very fast writes, high throughput
**Cons:** ❌ Risk of data loss on crash

## ⏰ 4. Write-Behind Cache

### 📮 **Scheduled Postal Service:**
Like having mail pickup at scheduled times (9 AM, 2 PM, 5 PM) regardless of mailbox status.

**How It Works:**
```
User changes product price:
Step 1: Write to Cache ($100)      ✅
Step 2: Confirm to user "Success!"
Step 3: Schedule database write for next interval

Database writes happen every 5 minutes, regardless
```

### 📊 **Write-Behind Flow:**
```
Application → Cache
     ↑         ↓ (immediate)
     ←─────────┘
     "Success!"

Cache → Database (every 5 minutes, scheduled)
```

**Perfect For:**
- Batch processing systems
- Analytics data
- Non-critical updates
- High-volume writes

**Pros:** ✅ Fast writes, predictable database load
**Cons:** ❌ Potential data loss, delayed consistency

---

## 🛠️ Cache Invalidation Methods

### 🗂️ **Library Book Management Analogy:**

```
Library Cache Management:
├── Purge: Remove specific book immediately
├── Refresh: Get latest edition of book
├── Ban: Mark entire shelf as outdated
├── TTL: Books expire after due date
└── Stale-while-revalidate: Give old book, order new one
```

## 🗑️ 1. Purge

### 🧹 **Spring Cleaning Analogy:**
Like throwing away specific expired items from your fridge immediately.

**How Purge Works:**
```
Product price changed from $80 → $100

Purge Request:
Step 1: DELETE cache entry for "product-123"
Step 2: Next request → Cache MISS
Step 3: Fetch from database ($100)
Step 4: Cache new price

Timeline: Immediate removal
```

**Use Cases:**
- Critical price updates
- Product deletions
- Security patches
- Emergency content removal

## 🔄 2. Refresh  

### 🍞 **Fresh Bread Delivery:**
Like baker bringing fresh bread to replace yesterday's bread in your store.

**How Refresh Works:**
```
Product description updated:

Refresh Request:
Step 1: Fetch NEW data from database
Step 2: REPLACE cached data with new version  
Step 3: Keep serving (no downtime)

Timeline: Updated immediately, no cache miss
```

**Use Cases:**
- Regular content updates
- Scheduled data refresh
- Proactive cache warming
- Content publishing systems

## 🚫 3. Ban

### 📋 **Do Not Serve List:**
Like restaurant having a "86'd list" - items marked as unavailable but still on menu until replaced.

**How Ban Works:**
```
Ban all holiday sale products:

Ban Command: "ban /category/holiday-sales/*"
Step 1: Add pattern to ban list
Step 2: DON'T remove items immediately
Step 3: On next request → Check ban list
Step 4: If matches → Fetch fresh + update cache

Timeline: Lazy invalidation, removed when requested
```

### 📊 **Purge vs Ban Comparison:**
```
Purge (Hard Delete):           Ban (Soft Delete):
[X] [X] [X] [X]               [?] [?] [?] [?]
Deleted immediately            Marked as invalid

Next request:                  Next request:  
Cache MISS → Slow             Check ban list → Fetch if banned

Use when: Need immediate       Use when: Can tolerate delay
```

**Use Cases:**
- Bulk content updates
- Category-wide changes
- Pattern-based invalidation
- Memory-efficient cleanup

## ⏰ 4. TTL (Time-To-Live)

### 🥛 **Milk Expiration Date:**
Like checking expiration date on milk carton before drinking.

**How TTL Works:**
```
Product cached at 10:00 AM with 1-hour TTL

Timeline:
10:00 AM → Cache product (TTL = 11:00 AM)
10:30 AM → Request → Check TTL → Still valid → Serve
11:01 AM → Request → Check TTL → EXPIRED → Fetch fresh

Automatic expiration, no manual intervention needed
```

### 📅 **TTL Strategy by Content:**
```
Content Type        TTL Value     Reason
────────────────────────────────────────────
User sessions       30 minutes    Security
Product prices      5 minutes     Accuracy  
Images             30 days        Rarely change
CSS/JS files       7 days         Version controlled
News articles      1 hour         Freshness needed
```

**Pros:** ✅ Automatic, predictable, simple
**Cons:** ❌ May serve stale data near expiry

## 🔄 5. Stale-While-Revalidate

### 🍕 **Pizza Delivery Analogy:**
Like eating leftover pizza while new pizza is being delivered - you get food now, fresh food later.

**How Stale-While-Revalidate Works:**
```
Request for product data:
Step 1: Serve cached data immediately (even if expired) 🍕
Step 2: Start background fetch from database 🚚
Step 3: Update cache with fresh data when ready ✨
Step 4: Next user gets fresh data

User experience: Always fast response
Data freshness: Eventually consistent
```

### ⚡ **Timeline Comparison:**
```
Normal Cache (expired):
Request → Check cache → EXPIRED → Fetch from DB (300ms) → Serve
User waits: 300ms

Stale-While-Revalidate:
Request → Serve stale (1ms) → Background fetch → Update cache
User waits: 1ms
Next user gets: Fresh data
```

**Perfect For:**
- News feeds
- Social media timelines
- Product recommendations  
- Non-critical data

**Pros:** ✅ Always fast response, good user experience
**Cons:** ❌ Temporarily stale data, more complex

---

## 🎯 **Decision Framework**

### 📊 **Strategy Selection Matrix:**

```
Data Criticality → Strategy:
├── Mission Critical (payments) → Write-Through + Purge
├── Important (product data) → Write-Around + Refresh
├── Performance Critical (gaming) → Write-Back + TTL
└── Batch Processing → Write-Behind + Ban

Access Pattern → Method:
├── Immediate consistency needed → Purge
├── Regular updates → Refresh  
├── Bulk changes → Ban
├── Predictable expiry → TTL
└── Fast response priority → Stale-while-revalidate
```

### 🛒 **E-commerce Example:**

```
Amazon Product Page Cache Strategy:

Product Price:
├── Strategy: Write-Through (consistency critical)
├── Method: Purge (immediate update needed)
└── Why: Wrong price = lost money

Product Images:
├── Strategy: Write-Around (rarely change after upload)
├── Method: TTL (30 days expiry)
└── Why: Performance over immediate consistency

User Reviews:
├── Strategy: Write-Back (high volume writes)
├── Method: Stale-while-revalidate (good UX)
└── Why: Reviews can be slightly delayed

Inventory Count:
├── Strategy: Write-Through (prevent overselling)
├── Method: Refresh (proactive updates)
└── Why: Stock accuracy critical
```

## 🎤 **Interview-Ready Answer**

**"Explain cache invalidation strategies":**

> "Cache invalidation solves the stale data problem - like checking milk expiration dates in your fridge. There are 4 write strategies:
>
> • **Write-Through** (bank deposit) - Update cache AND database simultaneously. Perfect consistency but slower.
> • **Write-Around** (direct mail) - Skip cache, write to database directly. Fast writes but cache misses.
> • **Write-Back** (notebook) - Write to cache first, database later. Fastest but risky.
> • **Write-Behind** (scheduled pickup) - Like write-back but on schedule.
>
> For invalidation methods: **Purge** removes immediately, **Refresh** updates proactively, **Ban** marks groups invalid, **TTL** expires automatically, and **Stale-while-revalidate** serves old data while fetching new.
>
> For e-commerce: Use write-through + purge for prices, write-around + TTL for images."

**Quick Decision:**
```
Critical data → Write-Through + Purge
High volume → Write-Back + TTL
Bulk updates → Write-Around + Ban
Good UX → Any + Stale-while-revalidate
```

## 📊 **Performance Impact**

```
Write Strategy Performance:
├── Write-Through: 2x slower writes, 0% data loss
├── Write-Around: 1x write speed, cache miss penalty
├── Write-Back: 10x faster writes, risk of data loss
└── Write-Behind: 5x faster writes, delayed consistency

Invalidation Method Speed:
├── Purge: Immediate (1ms)
├── Refresh: Immediate (database query time)
├── Ban: Lazy (when requested)
├── TTL: Automatic (background)
└── Stale-while-revalidate: Always fast (1ms)
```

## ⚠️ **Common Pitfalls**

```
❌ Using write-back for critical data
✅ Use write-through for financial data

❌ Setting TTL too long
✅ Balance freshness vs performance

❌ Purging everything on small changes  
✅ Use ban for bulk invalidation

❌ No invalidation strategy
✅ Always plan for data updates
```

---

## 📚 Source
[Design Gurus - Cache Invalidation](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/cache-invalidation)

## 🏷️ Tags
`#SystemDesign` `#Caching` `#DataConsistency` `#Performance` `#Interview`