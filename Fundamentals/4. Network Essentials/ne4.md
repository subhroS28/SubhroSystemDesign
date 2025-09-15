# 📍 URL vs URI vs URN - System Design

## 🎯 Core Difference (Simple Analogy)

### 🏠 **Address System Analogy**

| Concept | Real World | Web World | Purpose |
|---------|------------|-----------|---------|
| **📍 URI** | **Address System** (any way to identify) | **All identifiers** | "How do I identify something?" |
| **🗺️ URL** | **Street Address** ("123 Main St, NYC") | **Web address** (http://site.com) | "Where is it and how do I get there?" |
| **📛 URN** | **Person's Name** ("John Smith") | **Unique name** (isbn:123456) | "What is it called uniquely?" |

**Key Relationship:** All URLs and URNs are URIs, but not all URIs are URLs or URNs

---

## 📍 URI (Uniform Resource Identifier) - The Umbrella Term

### 📖 **What is URI?**
**Umbrella term** for any way to identify a resource - like saying **"address system"**

### 🎯 **Think of URI as "ID System"**
```
🆔 URI = Any way to identify something
├── 🗺️ URL: "Where it is" + "How to get it"
├── 📛 URN: "What it's called" (unique name)
└── 🔗 Other identifiers (custom schemes)
```

### 🌍 **Real Examples**
```
✅ All of these are URIs:
├── https://google.com (URL)
├── mailto:john@email.com (URL for email)
├── urn:isbn:0451450523 (URN for book)
├── tel:+1-555-123-4567 (URL for phone)
└── ftp://files.company.com/data.zip (URL for FTP)
```

---

## 🗺️ URL (Uniform Resource Locator) - Street Address

### 📖 **What is URL?**
Tells you **WHERE** something is and **HOW** to get it - like a **GPS address**

### 🎯 **URL Components (Address Parts)**
```
https://www.amazon.com:443/books/fiction?genre=mystery#reviews
│      │   │              │    │                        │
│      │   │              │    │                        └─ Fragment (like apartment #)
│      │   │              │    └─ Query params (like directions)
│      │   │              └─ Path (like street number)
│      │   └─ Domain (like street name)
│      └─ Subdomain (like neighborhood)
└─ Protocol (like transportation method)
```

### 🏠 **Real-World Analogy Breakdown**
```
🏠 Street Address: "Drive to 123 Main St, Apt 5B, NYC"
🌐 URL: "https://amazon.com/books/fiction#reviews"

Protocol = Transportation method:
├── 🚗 "Drive" = https://
├── 🚶‍♂️ "Walk" = ftp://
└── 📧 "Mail" = mailto:

Domain = Street address:
├── 🏠 "123 Main St" = amazon.com
├── 🏪 "456 Oak Ave" = google.com

Path = Specific location:
├── 🚪 "Apt 5B" = /books/fiction
├── 🏢 "Floor 3" = /search/results

Fragment = Exact spot:
├── 📍 "Kitchen" = #reviews
├── 🛏️ "Bedroom" = #description
```

### 🌍 **Common URL Examples**
```
🌐 Websites:
├── https://netflix.com/watch/12345
├── https://github.com/user/project

📧 Email:
├── mailto:support@company.com
├── mailto:john@email.com?subject=Hello

📁 Files:
├── ftp://files.company.com/downloads/app.zip
├── file:///C:/Documents/report.pdf

📞 Phone:
├── tel:+1-555-123-4567
├── sms:+1-555-987-6543
```

---

## 📛 URN (Uniform Resource Name) - Unique Name

### 📖 **What is URN?**
Gives something a **unique name** forever - like a **Social Security Number**

### 🎯 **URN Characteristics**
- **🏷️ Name only** - doesn't tell you where to find it
- **♾️ Permanent** - name never changes
- **🌍 Global** - unique across the entire world
- **📍 Location independent** - same name regardless of where it moves

### 🏠 **Real-World Analogy**
```
📛 Person's Name: "John Smith"
├── 🆔 Same person whether he lives in NYC or LA
├── 📍 Name doesn't tell you his address
├── ♾️ Name stays same his whole life
└── 🔍 Need to look up where he currently lives

📚 Book URN: "urn:isbn:0451450523"
├── 📖 Same book whether it's in Amazon or Barnes & Noble
├── 🏪 URN doesn't tell you which store has it
├── ♾️ ISBN never changes
└── 🔍 Need to search where to buy it
```

### 🌍 **Common URN Examples**
```
📚 Books:
├── urn:isbn:0451450523 (ISBN number)
├── urn:isbn:978-0134685991

🎵 Music:
├── urn:uuid:6ba7b810-9dad-11d1-80b4-00c04fd430c8
├── urn:spotify:track:4iV5W9uYEdYUVa79Axb7Rh

🔬 Scientific:
├── urn:doi:10.1000/182 (Digital Object Identifier)
├── urn:pmid:12345678 (PubMed ID)

🏛️ Legal:
├── urn:lex:us:federal:statute:26:501
├── urn:rfc:2396 (Internet RFC documents)
```

---

## 🔍 **URI vs URL vs URN Comparison**

### 📊 **Quick Comparison Table**

| Aspect | 📍 URI | 🗺️ URL | 📛 URN |
|--------|---------|---------|---------|
| **Purpose** | Identify anything | Locate + access | Name uniquely |
| **Tells you WHERE** | Maybe | ✅ Yes | ❌ No |
| **Tells you HOW** | Maybe | ✅ Yes | ❌ No |
| **Tells you WHAT** | ✅ Yes | ✅ Yes | ✅ Yes |
| **Changes location** | Depends | ❌ Breaks | ✅ Still works |
| **Example** | Any of the below | https://site.com | urn:isbn:123 |

### 🎯 **Relationship Diagram**
```
📍 URI (Universe of all identifiers)
├── 🗺️ URL (Location + How to access)
│   ├── https://google.com
│   ├── mailto:john@email.com
│   └── ftp://files.company.com
├── 📛 URN (Name only)
│   ├── urn:isbn:0451450523
│   ├── urn:uuid:123e4567-e89b-12d3
│   └── urn:doi:10.1000/182
└── 🔗 Other URI schemes
    ├── data:text/plain;base64,SGVsbG8=
    └── javascript:alert('Hello')
```

---

## 🌍 **Real-World Examples in Context**

### 📚 **Library System Example**

**Finding a book in different ways:**

```
📖 Book: "To Kill a Mockingbird"

📛 URN (Unique Name):
├── urn:isbn:0060935464
├── 🎯 Same book everywhere in the world
├── 📍 Doesn't tell you which library has it
└── ♾️ Never changes, even if reprinted

🗺️ URL (Where to find it):
├── https://library.harvard.edu/catalog/book/0060935464
├── 🏛️ Tells you it's at Harvard Library
├── 🌐 Tells you how to access it (HTTPS)
└── ⚠️ Changes if book moves to different library

📍 URI (Could be either):
├── The URN above (identifies by name)
├── The URL above (identifies by location)
└── Both are valid URIs!
```

### 🛒 **E-commerce Example**

**Amazon product in different formats:**

```
📱 iPhone 15 Pro:

📛 URN Approach:
├── urn:apple:iphone:15-pro:256gb:black
├── 🎯 Unique identifier that never changes
├── 📍 Doesn't tell you where to buy it
└── ✅ Same ID whether it's on Amazon, Best Buy, or Apple Store

🗺️ URL Approach:
├── https://amazon.com/dp/B0C3K4J7K2
├── 🛒 Tells you it's on Amazon
├── 🌐 Tells you how to access it (HTTPS)
└── ❌ Breaks if Amazon changes their URL structure

📍 URI (General term):
├── Could use either approach above
├── Amazon chose URLs for direct sales
└── URNs better for inventory management across stores
```

---

## 🎤 **Interview-Ready Summary**

### ❓ **"Explain the difference between URL, URI, and URN"**

**Perfect Answer:**
> *"Think of it like address systems. URI is the general concept of 'how to identify something' - like saying 'address system.' URL is like a street address - it tells you exactly where something is and how to get there ('Drive to 123 Main St'). URN is like a person's name - it uniquely identifies something but doesn't tell you where to find it ('John Smith' could live anywhere).
>
> For example: https://amazon.com/books is a URL (tells you where and how), urn:isbn:123456 is a URN (unique book name), and both are URIs (ways to identify resources). All URLs and URNs are URIs, but not all URIs are URLs or URNs."*

### ❓ **"When would you use URN vs URL?"**

**Decision Framework:**
```
Use URN when:
├── ✅ Need permanent, unchanging identifier
├── ✅ Resource might move between locations
├── ✅ Multiple access points exist
├── ✅ Academic/scientific references

Use URL when:
├── ✅ Need direct access to resource
├── ✅ Location is stable
├── ✅ Web browsing and APIs
├── ✅ Immediate access required
```

### ❓ **"Give me a real example of each"**

**Quick Examples:**
```
📍 URI (umbrella term):
├── "Any way to identify something"
└── Could be any of the below

🗺️ URL (location + access method):
├── https://youtube.com/watch?v=dQw4w9WgXcQ
├── mailto:john@company.com
└── ftp://files.server.com/download.zip

📛 URN (permanent unique name):
├── urn:isbn:0451450523 (book)
├── urn:uuid:123e4567-e89b-12d3 (unique ID)
└── urn:doi:10.1000/182 (research paper)
```

### ❓ **"Why don't we see URNs much on the web?"**

**Practical Reasons:**
```
🌐 Web favors URLs because:
├── 🎯 Direct access needed (click and go)
├── ⚡ Immediate results expected
├── 🔍 Search engines need crawlable links
├── 👥 Users understand "addresses"

📚 URNs better for:
├── 🏛️ Academic citations
├── 📖 Library systems  
├── 🔬 Scientific references
├── 💾 Database identifiers
```

---

## 🏷️ **Key Takeaways**

### 🎯 **Remember This:**
- **URI:** Umbrella term (address system)
- **URL:** Street address (where + how)
- **URN:** Person's name (unique identifier)
- **Relationship:** All URLs and URNs are URIs

### 📊 **Common Usage:**
- **Web browsing:** Almost always URLs
- **APIs:** Mostly URLs, some URN-style IDs
- **Academic papers:** URNs for citations
- **Databases:** Mixed (URLs for access, URNs for IDs)

### 🌍 **Real Examples:**
- **URL:** https://google.com, mailto:john@email.com
- **URN:** urn:isbn:123456, urn:uuid:abc-def-123
- **URI:** Any of the above (it's the umbrella term)

### 🎯 **For Interviews:**
- Know the address system analogy
- Understand the umbrella relationship (all URLs/URNs are URIs)
- Give concrete examples of each type
- Explain when to use each approach

---

## 📚 **Source**
[Design Gurus - URL vs URI vs URN](https://www.designgurus.io/course-play/grokking-system-design-fundamentals/doc/url-vs-uri-vs-urn)

## 🏷️ **Tags**
`#SystemDesign` `#URL` `#URI` `#URN` `#WebFundamentals` `#Identifiers` `#Interview`

---

> **💡 Pro Tip:** In daily web development, you mostly work with URLs. Think of URI as the umbrella term, URL as "address with directions," and URN as "permanent name tag." The address system analogy makes this crystal clear in interviews!