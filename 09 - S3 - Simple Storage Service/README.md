# 09 - S3 (Simple Storage Service)

![Phase-3 Storage](https://img.shields.io/badge/Phase-3%20Storage-green)
![Difficulty](https://img.shields.io/badge/Difficulty-%E2%AD%90%E2%AD%90%20Medium-yellow)
![Read Time](https://img.shields.io/badge/Read%20Time-~15%20min-blue)

---

## What is S3?

Hey Ravi! Welcome to **Phase 3 — Storage**! 🎉 Let's kick things off with one of the most fundamental AWS services: **Amazon S3 (Simple Storage Service)**.

S3 is **object storage** in the cloud. You can store and retrieve **any amount of data** from **anywhere** on the web. It's like Google Drive, but designed for developers — unlimited storage, 99.999999999% durability (11 nines!), and accessible via API.

> **Key Concept:** S3 stores data as **objects** inside **buckets**. Not files in folders — objects in buckets. This distinction matters!

---

## Why Do We Need S3?

| Problem | Traditional Storage | S3 Solution |
|---------|-------------------|-------------|
| Limited disk space | Buy bigger hard drives | Unlimited storage 💾 |
| Data access from anywhere | VPN or shared drives | Accessible via API/HTTP 🌐 |
| Backup & disaster recovery | Tape backups, manual | Automatic replication across AZs/regions 🔄 |
| Static website hosting | Need a web server | Host directly from S3 🌍 |
| Cost management | Pay for full capacity | Pay only for what you use 💰 |

---

## Real-World Analogy

Ravi, think of S3 like a **massive warehouse** 🏭:

- **Buckets** = Different rooms in the warehouse (Room A, Room B...)
- **Objects** = Boxes stored in rooms (each box has a unique label)
- **Key** = The label/ID on each box (how you find it later)
- **Metadata** = Notes on the box ("contains books", "fragile", "3 lbs")

Unlike a real warehouse, this one has:
- ♾️ Unlimited space
- 🌍 Access from anywhere in the world
- 🔒 Heavy-duty locks (encryption & access control)
- 📦 Automatic backup to other warehouses (replication)

> **Ravi's Note:** S3 is NOT a file system. There are no real "folders" — it's a flat structure with keys. But AWS makes it LOOK like folders for convenience!

---

## Core Concepts

### Buckets
- **Containers** for objects (like root folders)
- **Globally unique** names (across ALL of AWS)
- Created in a specific **region** (data stays in that region by default)
- **No sub-buckets** — but you can use "folder-like" prefixes

### Objects
Each object in S3 consists of:

| Component | Description |
|-----------|-------------|
| **Key** | Full path to the object (e.g., `images/photo.jpg`) |
| **Value** | The actual data (0 bytes to 5 TB) |
| **Metadata** | Key-value pairs (content-type, cache-control, etc.) |
| **Version ID** | Unique identifier (if versioning is enabled) |
| **Access Control** | Who can read/write this object |

> **Object Size Limit:** A single S3 object can be up to **5 TB**. For larger files, use **multipart upload**!

---

## Storage Classes

This is where S3 gets really interesting, Ravi. Different storage classes = different cost/performance tradeoffs:

| Storage Class | Use Case | Retrieval Time | Cost |
|--------------|----------|----------------|------|
| **S3 Standard** | Frequently accessed data | Instant | 💰💰💰 |
| **S3 Standard-IA** | Infrequent access (monthly) | Instant | 💰💰 |
| **S3 One Zone-IA** | Infrequent, non-critical | Instant | 💰 (cheapest IA) |
| **S3 Glacier Instant** | Archive, quick retrieval | Milliseconds | 💰 |
| **S3 Glacier Flexible** | Archive, minutes to hours | Minutes to hours | 💰 (very cheap) |
| **S3 Glacier Deep Archive** | Long-term archive | Hours | 💰 (cheapest overall) |

### Visual Cost vs Access Speed

```
Expensive & Fast ◄──────────────────────► Cheap & Slow

Standard → Std-IA → Glacier Instant → Glacier Flex → Deep Archive
  ⚡         ⚡          🔋               🔋             🐌
```

> **Pro Tip:** Use **Lifecycle Policies** to automatically move objects between classes. E.g., move to IA after 30 days, Glacier after 90 days, Deep Archive after 365 days. This saves MASSIVE costs!

---

## Key Features

### 1. Versioning
Keeps **multiple versions** of an object when it's modified or deleted.

```
s3://my-bucket/report.pdf  (version 1 - original)
s3://my-bucket/report.pdf  (version 2 - updated)
s3://my-bucket/report.pdf  (version 3 - latest)
```

- ✅ Protects against accidental deletes
- ✅ Allows restoring previous versions
- ⚠️ Once enabled, **can only be suspended** (not disabled)

### 2. Replication
Automatically copy objects to another bucket (same or different region).

| Type | Description |
|------|-------------|
| **S3 Replication (same-region)** | Copies within same region (compliance) |
| **S3 Cross-Region Replication** | Copies to different region (disaster recovery) |

### 3. Lifecycle Policies
Rules that automate object transitions between storage classes:

```yaml
# Example Lifecycle Rule
Rules:
  - ID: MoveToIA
    Transition:
      Days: 30
      StorageClass: STANDARD_IA
  - ID: MoveToGlacier
    Transition:
      Days: 90
      StorageClass: GLACIER
  - ID: DeleteOldObjects
    Expiration:
      Days: 365
```

### 4. Encryption

| Type | How It Works |
|------|-------------|
| **SSE-S3** | AWS manages keys (AES-256) |
| **SSE-KMS** | You manage keys via KMS (audit trail) |
| **SSE-C** | You provide keys (you manage everything) |
| **Client-Side** | You encrypt before uploading |

### 5. Versioning
(Already covered above — but worth repeating: it's a safety net!)

### 6. Access Control
Multiple ways to control who can access your data:

| Method | Description |
|--------|-------------|
| **Bucket Policies** | JSON policies attached to the bucket |
| **IAM Policies** | Control access for AWS users/roles |
| **ACLs** | Legacy per-object permissions (avoid if possible) |
| **Pre-signed URLs** | Temporary access URLs for specific objects |

---

## Bucket Policies vs ACLs

| Feature | Bucket Policy | ACL |
|---------|--------------|-----|
| **Scope** | Entire bucket | Individual objects |
| **Complexity** | More powerful | Simple but limited |
| **Control** | Fine-grained (resource, condition, principal) | Basic read/write per grantee |
| **Recommended** | ✅ Yes | ❌ No (use policies instead) |

> **Ravi's Rule:** Always prefer **Bucket Policies** and **IAM Policies** over ACLs. ACLs are legacy and harder to manage!

---

## S3 Static Website Hosting

Did you know S3 can host a website? 🌐

1. Create a bucket
2. Enable **Static Website Hosting**
3. Upload `index.html` and `error.html`
4. Set a **Bucket Policy** to allow public read
5. Access via the S3 website endpoint!

> **Best Practice:** For production, put **CloudFront** (CDN) in front of S3 for better performance and custom domain support.

---

## Architecture Overview

Here's how S3 fits into the bigger picture:

```
         ┌───────────────────────────────────────────────┐
         │                    AWS Cloud                    │
         │                                               │
         │  ┌──────────┐    ┌──────────────────────────┐ │
         │  │   User   │───►│    S3 Bucket              │ │
         │  │ (Browser)│    │    (us-east-1)            │ │
         │  │  / CLI   │    │                           │ │
         │  └──────────┘    │  ┌────────────────────┐   │ │
         │       │          │  │ objects/image.jpg   │   │ │
         │       │          │  │ objects/report.pdf  │   │ │
         │       │          │  │ objects/data.csv    │   │ │
         │       │          │  └────────────────────┘   │ │
         │       │          │                           │ │
         │       │          │  Lifecycle Policy:        │ │
         │       │          │  30d → IA                  │ │
         │       │          │  90d → Glacier             │ │
         │       │          └──────────────────────────┘ │
         │       │                    │                   │
         │       │              Versioning                │
         │       │              Replication               │
         │       │              Encryption                │
         │  ┌────▼──────┐                                │
         │  │ CloudFront│  (Optional: CDN for speed)     │
         │  └───────────┘                                │
         └───────────────────────────────────────────────┘
```

---

## Common Use Cases

| Use Case | Description |
|----------|-------------|
| **Backup & Restore** | Store database backups, snapshots |
| **Static Website Hosting** | HTML/CSS/JS sites |
| **Data Lake** | Store raw data for analytics |
| **Media Storage** | Images, videos, audio files |
| **Log Storage** | Application and access logs |
| **Software Distribution** | Store installers, updates |
| **Disaster Recovery** | Cross-region replication |
| **Big Data Analytics** | Input/output for EMR, Athena, Redshift |

---

## Best Practices

1. 🔒 **Block public access by default** — AWS now does this automatically for new buckets!
2. 🔐 **Enable encryption** — Use SSE-KMS for better audit trails
3. 🏷️ **Tag everything** — For cost allocation and management
4. 🔄 **Enable versioning** — Protect against accidental deletions
5. 📋 **Use lifecycle policies** — Automate storage class transitions
6. 📊 **Enable access logging** — Track who accessed what
7. 🌐 **Use CloudFront** — For static websites and content delivery
8. 🔑 **Use Bucket Policies wisely** — Deny public access unless explicitly needed
9. 💰 **Monitor costs** — S3 costs can sneak up with many objects

---

## Common Mistakes

| Mistake | Impact | Fix |
|---------|--------|-----|
| Making buckets public accidentally | Data breach 😱 | Block public access by default |
| Not enabling versioning | Accidental deletes = permanent loss 💔 | Enable versioning on important buckets |
| Ignoring storage classes | Overpaying for rarely accessed data 💸 | Use lifecycle policies to transition |
| No lifecycle policies | Old data accumulating at Standard rates 💰 | Auto-transition to cheaper classes |
| Not encrypting sensitive data | Compliance violations ⚠️ | Use SSE-KMS encryption |
| Sharing access keys in code | Credentials leaked to repo 🔓 | Use IAM roles, environment variables |
| No access logging | Can't audit who accessed data 🔍 | Enable server access logging |

---

## Interview Questions

### Q1: What is the difference between a Bucket Policy and an IAM Policy?
**Answer:** A **Bucket Policy** is attached to an S3 bucket and controls who can access that specific bucket (resource-based). An **IAM Policy** is attached to a user/role and controls what that user/role can access (identity-based). Bucket policies are great for granting cross-account access, while IAM policies are better for managing individual user permissions.

### Q2: What is S3 versioning and why is it important?
**Answer:** Versioning keeps multiple variants of an object in the same bucket. When you overwrite or delete an object, S3 keeps the previous version. This protects against accidental deletions and overwrites. Once enabled, versioning can only be suspended (never fully disabled). Objects with versions consume more storage.

### Q3: Explain S3 Storage Classes and when to use each.
**Answer:** **Standard** for frequently accessed data. **Standard-IA** for data accessed less often (monthly) — cheaper but has retrieval fees. **Glacier Instant Retrieval** for archive that needs millisecond access. **Glacier Flexible** for archive where hours of retrieval time is OK. **Deep Archive** for long-term compliance data accessed rarely (12+ hours retrieval). Use **Lifecycle Policies** to automate transitions between classes.

### Q4: How does S3 ensure data durability?
**Answer:** S3 Standard provides **99.999999999% durability (11 nines)**. It achieves this by automatically storing data across **at least 3 Availability Zones** within a region. Even if two AZs are lost simultaneously, your data is still safe. Cross-Region Replication can further protect against regional disasters.

### Q5: What is the maximum size of a single S3 object and how do you handle larger files?
**Answer:** A single S3 object can be up to **5 TB**. For files larger than 5 GB, you **must** use **Multipart Upload** — which splits the file into parts, uploads them in parallel, then reassembles on the server side. AWS SDKs handle this automatically for large files. Multipart upload also improves reliability (if one part fails, only that part needs retry).

---

## Summary

| Concept | Key Takeaway |
|---------|-------------|
| **S3** | Unlimited object storage (any file type) |
| **Buckets** | Containers for objects (globally unique names) |
| **Objects** | Data + metadata + unique key |
| **Storage Classes** | Standard → IA → Glacier → Deep Archive |
| **Versioning** | Keep all versions, protect against deletes |
| **Lifecycle Policies** | Automate storage class transitions |
| **Encryption** | Always encrypt sensitive data |
| **Bucket Policies** | Control access (preferred over ACLs) |

---

**Next:** [10 - EFS and Storage Gateway](../10%20-%20EFS%20and%20Storage%20Gateway/README.md) → Explore shared file storage and hybrid cloud solutions! 🗂️
