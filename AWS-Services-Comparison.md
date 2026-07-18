# 📋 AWS Services — When to Use What?

> *"Ravi, interviewers LOVE asking 'When would you use X vs Y?' This guide is your cheat code."* — **Rithu** 🎯

---

## 🗄️ Storage: S3 vs EBS vs EFS

| Feature | S3 | EBS | EFS |
|:--------|:---|:----|:----|
| **Type** | Object Storage | Block Storage | File Storage |
| **Access** | HTTP/API | Mounted to ONE EC2 | Mounted to MULTIPLE EC2s |
| **Use For** | Files, backups, static sites, data lakes | OS boot disk, databases | Shared config files, CMS |
| **Scalability** | Unlimited | Up to 64 TB | Auto-scales |
| **Analogy** | Google Drive | USB Flash Drive | Shared Network Drive |
| **Cost Model** | Pay per GB stored | Pay per GB-month | Pay per GB-month |

### Quick Decision:
```
Need to store files globally?           → S3
Need a hard drive for EC2?              → EBS
Need multiple EC2s to share one folder?  → EFS
```

---

## 🗄️ Database: RDS vs DynamoDB

| Feature | RDS | DynamoDB |
|:--------|:----|:---------|
| **Type** | Relational (SQL) | NoSQL (Key-Value) |
| **Schema** | Fixed schema (tables, columns) | Flexible schema |
| **Scaling** | Vertical (bigger instance) | Horizontal (auto-scales) |
| **Query** | SQL (JOIN, GROUP BY) | API calls (GetItem, Query) |
| **Use For** | Complex queries, transactions, reporting | High-speed reads/writes, gaming, IoT |
| **Analogy** | Excel spreadsheet | JSON document store |
| **Best Example** | E-commerce orders | User session data, leaderboards |

### Quick Decision:
```
Need complex JOINs or transactions?      → RDS
Need microsecond reads at any scale?      → DynamoDB
Need ACID compliance?                    → RDS
Building a serverless app?               → DynamoDB
```

---

## ⚖️ Load Balancer: ALB vs NLB

| Feature | ALB | NLB |
|:--------|:----|:----|
| **Layer** | Layer 7 (HTTP/HTTPS) | Layer 4 (TCP/UDP) |
| **Use For** | Web apps, REST APIs | Databases, gaming, IoT |
| **Features** | Path routing, host routing, SSL | Ultra-low latency, static IP |
| **Health Checks** | HTTP-based | TCP-based |
| **Cost** | Cheaper | More expensive |

### Quick Decision:
```
Web app or API?              → ALB
Database or gaming server?   → NLB
Need static IP?              → NLB
Need path-based routing?     → ALB
```

---

## 🌐 Networking: Security Groups vs NACLs

| Feature | Security Group | NACL |
|:--------|:---------------|:-----|
| **Level** | Instance-level | Subnet-level |
| **State** | Stateful (auto-allows responses) | Stateless (must allow both directions) |
| **Rules** | Only ALLOW rules | ALLOW and DENY rules |
| **Default** | Deny all inbound, allow all outbound | Allow all inbound and outbound |
| **Applies To** | Individual instances | All instances in subnet |

### Quick Decision:
```
Need to control traffic to ONE instance?  → Security Group
Need to block a specific IP at subnet level? → NACL
Default choice?                          → Security Group (simpler)
```

---

## 📨 Messaging: SNS vs SQS vs EventBridge

| Feature | SNS | SQS | EventBridge |
|:--------|:----|:----|:------------|
| **Pattern** | Pub/Sub (push) | Queue (pull) | Event Router |
| **Delivery** | Push to subscribers | Consumer pulls messages | Rule-based routing |
| **Use For** | Alerts, fan-out notifications | Decoupling, background jobs | Event-driven architectures |
| **Analogy** | WhatsApp broadcast | Email inbox | Smart receptionist |
| **One-to-Many** | Yes (fan-out) | No (one consumer) | Yes (rules) |

### Quick Decision:
```
Need to send alerts to multiple services? → SNS
Need to decouple producers/consumers?     → SQS
Need complex event routing rules?         → EventBridge
```

---

## ⚡ Compute: EC2 vs Lambda

| Feature | EC2 | Lambda |
|:--------|:----|:-------|
| **Type** | Virtual server | Serverless function |
| **Management** | You manage OS, patches, scaling | AWS manages everything |
| **Scaling** | Manual or ASG | Auto-scales to thousands |
| **Pricing** | Per hour (even if idle) | Per request + duration |
| **Use For** | Long-running apps, legacy apps | Event-driven, short tasks |
| **Max Time** | Unlimited | 15 minutes |
| **Analogy** | Owning a car | Uber (pay per ride) |

### Quick Decision:
```
Need full control over OS?       → EC2
Need event-driven processing?    → Lambda
Long-running task (>15 min)?     → EC2
Short task with burst traffic?   → Lambda
```

---

## 🔐 Secrets: Secrets Manager vs Parameter Store

| Feature | Secrets Manager | Parameter Store |
|:--------|:----------------|:----------------|
| **Auto-Rotation** | Yes (built-in) | No (manual) |
| **Cost** | $0.40/secret/month + API calls | Free tier available |
| **Use For** | Database passwords, API keys | Config values, non-sensitive data |
| **Encryption** | KMS | KMS or tiers |
| **Hierarchy** | Flat | Hierarchical (folders) |

### Quick Decision:
```
Need automatic secret rotation?     → Secrets Manager
Storing config or non-sensitive?    → Parameter Store
Budget-conscious?                   → Parameter Store
```

---

## 💾 Backup: Snapshots vs AWS Backup

| Feature | Manual Snapshots | AWS Backup |
|:--------|:-----------------|:-----------|
| **Scope** | Individual service | All services in one place |
| **Automation** | Manual or scheduled | Centralized policy |
| **Lifecycle** | Manual cleanup | Auto-tier to Glacier |
| **Compliance** | Hard to enforce | Vault Lock for immutability |

---

## 🏗️ IaC: CloudFormation vs Terraform

| Feature | CloudFormation | Terraform |
|:--------|:---------------|:----------|
| **Cloud** | AWS only | Multi-cloud (AWS, Azure, GCP) |
| **Language** | YAML/JSON | HCL (HashiCorp Config Language) |
| **State** | Managed by AWS | Self-managed (S3 backend) |
| **Cost** | Free | Free (but Terraform Cloud is paid) |
| **Community** | AWS official | Large open-source community |

---

> *"Ravi, these comparisons are the #1 thing interviewers ask about. Bookmark this page."* — **Rithu** 🔖
