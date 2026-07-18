# 14 - DynamoDB (NoSQL Database)

![Phase-5-Databases](https://img.shields.io/badge/Phase-5-Databases-pink) ![Difficulty-Medium](https://img.shields.io/badge/Difficulty-⭐⭐-Medium-yellow) ![ReadTime](https://img.shields.io/badge/Read%20Time-~12%20min-blue)

> *"Ravi, what if you need a database that handles MILLIONS of requests per second with single-digit millisecond latency — and you don't want to manage a single server?"*
> *"That's... not possible, right?"*
> *"Meet DynamoDB — the vending machine of databases!"* 🎰

---

## DynamoDB Global Tables

![DynamoDB Global Tables](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/global-tables.png)

> *DynamoDB Global Tables provide multi-active replication across multiple AWS Regions.*

---

## What is DynamoDB?

**Amazon DynamoDB** is a fully managed **NoSQL key-value and document database**. It's designed for:

- ⚡ **Single-digit millisecond latency** at any scale
- 📈 **Millions of requests per second**
- 🌍 **Multi-region, multi-active** replication
- 🔧 **Zero server management** — completely serverless

### Core Terminology

| Term | Equivalent | Example |
|------|-----------|---------|
| **Table** | A collection of data | `Users` table |
| **Item** | A single record (row) | One user's data |
| **Attribute** | A field (column) | `Name`, `Email`, `Age` |

---

## Why Do We Need DynamoDB?

Ravi, imagine building an app like **Instagram**:

- Millions of users posting simultaneously
- Each post needs to load in **milliseconds**
- Traffic spikes during events (World Cup, New Year's Eve)
- You need it to work **globally**

A traditional relational database (RDS) would struggle with this scale. DynamoDB is **purpose-built** for:

- 🚀 Massive scale with consistent performance
- 📊 Flexible schema (no rigid table structure)
- 🔄 Real-time data changes (via DynamoDB Streams)
- 🌐 Multi-region replication (Global Tables)

---

## Real-World Analogy

Ravi, think of DynamoDB as a **vending machine** 🎰:

- You **press a button** (primary key) → You get a **snack instantly** (data)
- No waiting in line (no query parsing)
- No matter how many people use it, each person gets their item in **seconds**
- The machine **automatically restocks** (auto-scaling)
- You can add **new snack rows** anytime (schema flexibility)

Compared to a **restaurant** (relational DB): you sit down, wait for a waiter, order, wait for the food... delicious, but slower for simple lookups!

---

## Primary Key Design (CRITICAL!)

The primary key is **everything** in DynamoDB. It determines:

- ⚡ How fast your queries run
- 📊 How data is distributed across storage
- 🔄 What query patterns are supported

### Two Types of Primary Keys

| Key Type | Structure | Example |
|----------|-----------|---------|
| **Simple (Partition Key)** | Just one attribute | `UserID` |
| **Composite (Partition + Sort Key)** | Two attributes | `UserID` + `Timestamp` |

### Example: Composite Key Table

```
Users Table:
┌─────────────┬──────────────┬──────────┬────────┐
│ PartitionKey│ SortKey      │ Name     │ Email  │
├─────────────┼──────────────┼──────────┼────────┤
│ user101     │ 2024-01-15   │ Ravi     │ r@x.com│
│ user101     │ 2024-02-20   │ Ravi     │ r@x.com│
│ user102     │ 2024-01-10   │ Rithu    │ ri@x.com│
└─────────────┴──────────────┴──────────┴────────┘
```

- **Partition Key** = `UserID` (determines data distribution)
- **Sort Key** = `Timestamp` (allows range queries within a partition)
- Query: *"Get all posts by user101, sorted by date"* ✅

---

## Capacity Modes

| Mode | How You Pay | Best For |
|------|------------|----------|
| **On-Demand** | Per request (read/write) | Unpredictable traffic, new apps |
| **Provisioned** | Reserved capacity per hour | Predictable traffic, cost optimization |

### Read/Write Capacity Units (Provisioned Mode)

| Unit | What It Means |
|------|--------------|
| **RCU (Read Capacity Unit)** | 1 strongly consistent read/sec for items up to 4KB |
| **WCU (Write Capacity Unit)** | 1 write/sec for items up to 1KB |

---

## Key Features

### DAX (DynamoDB Accelerator)

**DAX** is an in-memory caching layer for DynamoDB that delivers **microsecond latency** for read-heavy workloads.

```
App ──► DAX Cache (microseconds) ──► DynamoDB (milliseconds)
       (First read: miss → DynamoDB)
       (Subsequent reads: hit → instant!)
```

### Global Tables

**Global Tables** provide **multi-region, multi-active** replication. Users in Tokyo and New York both get fast local reads.

| Feature | Standard Tables | Global Tables |
|---------|----------------|---------------|
| Regions | Single region | Multiple regions |
| Replication | None | Multi-region, multi-active |
| Conflict Resolution | N/A | Last Writer Wins |
| Use Case | Regional apps | Global apps |

### DynamoDB Streams

**Streams** capture a **time-ordered log of changes** to your table data.

```
Table Update ──► Stream ──► Lambda Function ──► Send Notification
                                               Update Search Index
                                               Replicate to another table
```

| Stream Enabled | What Happens |
|---------------|-------------|
| New item added | Captured in stream |
| Item updated | Both old and new values captured |
| Item deleted | Deletion captured |
| Retention | 24 hours |

### TTL (Time to Live)

Automatically **deletes expired items** — no application code needed.

- Example: Session data expires after 24 hours
- No additional cost for TTL
- Reduces storage costs and keeps tables clean

---

## RDS vs DynamoDB Comparison

| Feature | RDS | DynamoDB |
|---------|-----|----------|
| Type | Relational (SQL) | NoSQL (Key-Value/Document) |
| Schema | Fixed schema (tables, columns) | Flexible schema |
| Scaling | Vertical + Read Replicas | Horizontal, automatic |
| Queries | Complex JOINs, aggregations | Primary key lookups, scans |
| Latency | Milliseconds to seconds | Single-digit milliseconds |
| Max Item Size | No row limit | 400KB per item |
| Transaction Support | Full ACID | Limited transactions (TransactWrite) |
| Best For | Complex queries, reporting | High-speed key-value lookups |
| Management | Some (patching, scaling) | Fully serverless |

### When to Choose Which?

| Choose RDS When... | Choose DynamoDB When... |
|-------------------|----------------------|
| You need complex JOINs | You need simple key-based lookups |
| Your schema is rigid | Your schema changes frequently |
| You need ad-hoc SQL queries | You need consistent, predictable performance |
| You're building reporting/analytics | You're building real-time apps at scale |
| Data relationships are complex | Data is relatively flat |

---

## Best Practices for DynamoDB

- ✅ **Design primary keys carefully** — this is the #1 most important decision
- ✅ **Use sort keys** when you need range queries (e.g., time-series data)
- ✅ **Use DAX** for read-heavy workloads needing microsecond latency
- ✅ **Enable backups** — DynamoDB has point-in-time recovery (PITR)
- ✅ **Use Global Tables** for multi-region active-active applications
- ✅ **Monitor with CloudWatch** — track consumed capacity, throttles, errors
- ✅ **Use DynamoDB Streams** for event-driven architectures (with Lambda)
- ✅ **Set TTL** for data that expires — saves storage and cost
- ✅ **Use BatchWrite/BatchGet** for bulk operations (max 25 items per batch)

---

## Common Mistakes

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Poor partition key design | "Hot partition" — one partition gets all traffic | Choose a key with high cardinality (many unique values) |
| Using Scan instead of Query | Scan reads EVERY item (slow, expensive) | Always use Query with primary key conditions |
| Ignoring sort key design | Can't do range queries efficiently | Use sort keys for time-series or ordered data |
| Not enabling PITR | Can't recover from accidental deletes | Enable Point-in-Time Recovery |
| Oversizing items | 400KB limit per item | Store large objects in S3, reference in DynamoDB |
| Ignoring capacity costs | On-demand can be expensive at scale | Use provisioned mode with auto-scaling for predictable workloads |

---

## Interview Questions

### Q1: What is the difference between a Query and a Scan in DynamoDB?
**Answer:** A **Query** finds items using the **primary key** (partition key + optional sort key condition) — it's fast and efficient (O(1)). A **Scan** reads **every item** in the table — it's slow and expensive (O(n)). Always use Query. Use Scan only for small tables or debugging.

### Q2: How does DynamoDB handle scaling?
**Answer:** DynamoDB scales **horizontally** by automatically partitioning data across multiple servers. With **On-Demand mode**, it handles unlimited traffic. With **Provisioned mode**, you set capacity and can use **Auto Scaling** to adjust. Storage scales automatically with no size limit.

### Q3: Explain DynamoDB Streams and when you'd use them.
**Answer:** DynamoDB Streams capture **time-ordered changes** (inserts, updates, deletes) to a table, retained for 24 hours. Use cases: (1) Trigger **Lambda functions** on data changes, (2) Replicate data to another table, (3) Build **real-time analytics**, (4) Implement **event-driven architectures**.

### Q4: What is a hot partition and how do you avoid it?
**Answer:** A **hot partition** occurs when one partition receives disproportionately more traffic than others, causing throttling. Causes: (1) Using a partition key with low cardinality (e.g., "status" with few values), (2) Timestamps as partition keys. **Fix:** Use high-cardinality keys (UUIDs, user IDs), add random suffixes, or redesign the key.

### Q5: When should you choose DynamoDB over RDS?
**Answer:** Choose DynamoDB for: (1) Simple key-value lookups at massive scale, (2) Apps needing consistent single-digit ms latency, (3) Flexible schemas that evolve quickly, (4) Serverless architectures with Lambda, (5) Real-time apps with DynamoDB Streams. Choose RDS for complex SQL queries, JOINs, reporting, and traditional relational data.

---

## Summary

| Concept | Key Takeaway |
|---------|-------------|
| **DynamoDB** | Fully managed NoSQL key-value/document database |
| **Serverless** | Zero servers to manage |
| **Primary Key** | Partition Key (+ optional Sort Key) — design carefully! |
| **On-Demand** | Pay per request, no capacity planning |
| **Provisioned** | Reserved capacity, lower cost for predictable workloads |
| **DAX** | In-memory cache for microsecond reads |
| **Global Tables** | Multi-region, multi-active replication |
| **Streams** | Change capture for event-driven architectures |
| **TTL** | Auto-delete expired items |
| **Query vs Scan** | Always Query — Scan reads everything (expensive) |

---

> 📝 **Rithu says:** *"Ravi, DynamoDB is AWS's secret weapon for scale. But remember — the primary key is EVERYTHING. Design it right, and DynamoDB will fly. Design it wrong, and you'll hit hot partitions and throttling. Think twice before you create that table!"*

---

**Next:** [15 - CloudWatch](../15%20-%20CloudWatch/README.md) → Let's learn about monitoring in AWS! 📊
