# 🛡️ 23 - Security, Cost Optimization & Interview Q&A

![Phase](https://img.shields.io/badge/Phase-10%20Governance-gray)
![Difficulty](https://img.shields.io/badge/Difficulty-%E2%AD%90%E2%AD%90%E2%AD%90%20Comprehensive-red)
![Read Time](https://img.shields.io/badge/Read%20Time-~15%20min-blue)

> **Rithu:** "Ravi, you've learned so many AWS services! Now let's talk about the two things every company cares about most — keeping things **secure** and not wasting **money**. Plus, I'll prep you for interviews!"
>
> **Ravi:** "Security AND cost? That sounds like a lot..."
>
> **Rithu:** "Don't worry — we'll break it down piece by piece!"

---

## 📑 Table of Contents

- [Section 1 — Security Best Practices](#section-1--security-best-practices)
- [Section 2 — Cost Optimization](#section-2--cost-optimization)
- [Section 3 — Top Interview Q&A](#section-3--top-interview-qa)
- [Summary](#-summary)

---

# Section 1 — Security Best Practices 🔐

---

## AWS Security Overview

![AWS Security Reference Architecture](http://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/images/sra.png)

> *AWS Security Reference Architecture showing the comprehensive security services and their relationships.*

---

## What is AWS Security?

AWS Security is a set of **practices, tools, and services** that protect your cloud resources from unauthorized access, data leaks, and attacks.

## Why Do We Need It?

One misconfigured S3 bucket can expose **millions of records**. Security isn't optional — it's **job #1**.

## Real-World Analogy 🏠

> Think of your AWS account like a **house**:
> - You need **locks on doors** (IAM + MFA)
> - You need a **fence around the property** (VPC + Security Groups)
> - You need **cameras to monitor** (CloudTrail + GuardDuty)
> - You need a **safe for valuables** (KMS encryption)
> - You need **insurance** (compliance + audit logs)

---

## 🧅 Defense in Depth (Multiple Layers)

| Layer | What It Protects | AWS Service |
|-------|-----------------|-------------|
| **Identity** | Who can access | IAM, MFA |
| **Perimeter** | Network boundary | WAF, Shield, VPC |
| **Network** | Traffic control | Security Groups, NACLs |
| **Data** | The actual data | KMS, S3 encryption |
| **Monitoring** | Detect threats | CloudTrail, GuardDuty |

> **Rithu:** "Security is like an onion, Ravi — multiple layers, and if anyone peels one, there's another underneath!"

---

## 🔑 IAM Best Practices

| Practice | Why |
|----------|-----|
| **Least Privilege** | Give only the minimum permissions needed |
| **No Root Usage** | Root has FULL access — never use it for daily tasks |
| **Enable MFA** | Password + phone = 2x harder to hack |
| **Use Roles** | Not access keys when possible (EC2, Lambda) |
| **Rotate Keys** | Old keys = easy targets |

```
✅ Good: User can only read from a specific S3 bucket
❌ Bad:  User has AdministratorAccess for "just in case"
```

---

## 🔒 Encryption

### At Rest (data sitting on disk)
- Use **AWS KMS** to encrypt EBS, S3, RDS, Lambda env vars
- Default: AWS-managed keys (free)
- Custom: Customer-managed keys ($1/month per key)

### In Transit (data moving over network)
- Use **TLS/SSL** everywhere
- Enforce HTTPS on S3 buckets
- Use ACM (AWS Certificate Manager) for free SSL certs

> **Ravi:** "So encrypt it when it's sleeping AND when it's walking?"
>
> **Rithu:** "Exactly! 😄"

---

## 🌐 Network Security

| Component | What It Does |
|-----------|-------------|
| **Private Subnets** | Instances not directly reachable from internet |
| **Security Groups** | Instance-level firewall (stateful) |
| **NACLs** | Subnet-level firewall (stateless) |
| **VPC Flow Logs** | Log all network traffic for auditing |
| **AWS WAF** | Protect against web attacks (SQL injection, XSS) |

---

## 🔍 Monitoring & Detection

| Tool | Purpose |
|------|---------|
| **CloudTrail** | Who did what, when? (API audit log) |
| **GuardDuty** | AI-powered threat detection |
| **AWS Config** | Are resources compliant with rules? |
| **Amazon Inspector** | Vulnerability scanning for EC2 & ECR |
| **Security Hub** | Central dashboard for all security findings |

---

## 🔐 Secrets Management

```
❌ NEVER do this:
DB_PASSWORD = "SuperSecret123"   # hardcoded in code!

✅ ALWAYS do this:
DB_PASSWORD = secrets.get_secret("prod/db/password")
```

- **AWS Secrets Manager** — Auto-rotate secrets, encrypt at rest
- **Parameter Store (SSM)** — Free tier, store config and secrets

---

## 📋 Compliance

| Need | AWS Tool |
|------|----------|
| Audit trail | CloudTrail |
| Config compliance | AWS Config Rules |
| Encryption standards | KMS + bucket policies |
| Access reviews | IAM Access Analyzer |

---

# Section 2 — Cost Optimization 💰

---

## What is Cost Optimization?

Getting the **most value** from your AWS spend — not the least spend, but the **best spend**.

## Why Do We Need It?

AWS bills can spiral **fast**. A forgotten EC2 instance running 24/7 for a year costs ~$3,000+!

## Real-World Analogy 🛒

> It's like grocery shopping:
> - Buy in bulk (Reserved Instances) for things you always need
> - Get discounted items (Spot Instances) when you're flexible
> - Stop buying food you won't eat (delete unused resources)
> - Check your receipt regularly (Cost Explorer)

---

## 💡 Key Cost Optimization Strategies

### 1. Right-Sizing Instances

| Problem | Fix |
|---------|-----|
| t3.xlarge running at 5% CPU | Downsize to t3.micro |
| Database overloaded | Upgrade instance or add read replicas |

Use **AWS Compute Optimizer** — it recommends the right size automatically!

---

### 2. Reserved Instances & Savings Plans 💵

| Type | Commitment | Savings |
|------|-----------|---------|
| **1-Year RI** | 1 year | ~30-40% |
| **3-Year RI** | 3 years | ~50-60% |
| **Savings Plans** | Flexible $/hr commitment | ~30-72% |

> Best for: **predictable workloads** that run 24/7 (databases, web servers)

---

### 3. Spot Instances 🎯

- Up to **90% off** On-Demand pricing
- Can be **terminated** with 2-minute notice
- Perfect for: batch jobs, CI/CD, rendering, data processing

```
✅ Great: "Process these 10,000 images — restart if interrupted"
❌ Terrible: "Run my production database 24/7"
```

---

### 4. Auto Scaling — Scale Down

| Time | Strategy |
|------|----------|
| Business hours | Scale UP (more users) |
| Night/Weekend | Scale DOWN (save money) |

Auto Scaling doesn't just scale UP for traffic — it also **scales DOWN** to save!

---

### 5. S3 Lifecycle Policies 📦

| Age | Move To | Cost Savings |
|-----|---------|-------------|
| 0-90 days | S3 Standard | Full access |
| 90-180 days | S3 Infrequent Access | ~40% cheaper |
| 180+ days | S3 Glacier | ~95% cheaper |
| 365+ days | Glacier Deep Archive | ~99% cheaper |

---

### 6. Delete Unused Resources 🧹

| Resource | What to Check |
|----------|--------------|
| **Elastic IPs** | Not attached to running instances (charged!) |
| **Unattached EBS** | Old volumes sitting idle |
| **Old Snapshots** | Old backups you no longer need |
| **Idle Load Balancers** | ALBs with zero traffic |
| **Old AMIs** | Outdated machine images |

---

### 7. Cost Monitoring Tools 🔍

| Tool | What It Does |
|------|-------------|
| **Cost Explorer** | Visualize and forecast spending |
| **Budgets** | Set alerts when spending exceeds threshold |
| **Cost & Usage Reports** | Granular billing data (CSV) |
| **Trusted Advisor** | Cost optimization recommendations |
| **Compute Optimizer** | Right-sizing recommendations |

---

# Section 3 — Top Interview Q&A 🎤

---

## Q1: What is the difference between Security Groups and NACLs?

| Feature | Security Groups | NACLs |
|---------|----------------|-------|
| **Level** | Instance | Subnet |
| **State** | Stateful | Stateless |
| **Rules** | Allow only | Allow + Deny |
| **Default** | Deny all inbound | Allow all |

> **Answer:** Security Groups are **instance-level**, **stateful**, and only support **allow** rules. NACLs are **subnet-level**, **stateless**, and support both **allow and deny** rules. Security Groups are the most common choice.

---

## Q2: Compare S3, EBS, and EFS

| Feature | S3 | EBS | EFS |
|---------|-----|-----|-----|
| **Type** | Object | Block | File |
| **Access** | HTTP/API | Single EC2 | Multi-EC2 |
| **Use Case** | Static files, backups | Boot volumes, databases | Shared file storage |
| **Scalability** | Unlimited | Limited by volume | Auto-scales |

> **Answer:** S3 is object storage accessed via API, EBS is block storage attached to a single EC2 instance, and EFS is shared file storage accessible by multiple EC2 instances simultaneously.

---

## Q3: When would you use Lambda vs EC2?

| Factor | Lambda | EC2 |
|--------|--------|-----|
| **Duration** | < 15 min | Unlimited |
| **Scaling** | Automatic | Manual/ASG |
| **Management** | Serverless | You manage OS |
| **Cost** | Pay per invocation | Pay per hour |
| **Use Case** | Event-driven tasks | Long-running apps |

> **Answer:** Use Lambda for event-driven, short-lived tasks (API handlers, file processors). Use EC2 for long-running applications, custom software needs, or when you need full OS control.

---

## Q4: What is the difference between ELB and Auto Scaling Group?

> **Answer:** **ELB** distributes incoming traffic across targets. **ASG** automatically adjusts the number of EC2 instances. Together, ELB handles **traffic distribution** while ASG handles **capacity management**. ELB tells requests WHERE to go; ASG tells AWS HOW MANY instances to run.

---

## Q5: How do you secure an S3 bucket?

> **Answer:**
> 1. Block public access (default)
> 2. Use bucket policies with least privilege
> 3. Enable encryption (SSE-S3 or SSE-KMS)
> 4. Enable versioning
> 5. Enable access logging
> 6. Use VPC endpoints for private access

---

## Q6: What is Multi-AZ vs Multi-Region?

| Feature | Multi-AZ | Multi-Region |
|---------|----------|-------------|
| **Purpose** | High Availability | Disaster Recovery + Low Latency |
| **Distance** | Same region, different AZs | Different geographic regions |
| **Failover** | Automatic (RDS) | Manual or Route 53 |
| **Cost** | Moderate | High |

> **Answer:** Multi-AZ keeps your app available if one AZ fails. Multi-Region protects against an entire region going down and puts resources closer to global users.

---

## Q7: What is CloudFormation?

> **Answer:** CloudFormation is AWS's **Infrastructure as Code (IaC)** service. You write templates (YAML/JSON) that define your infrastructure. Benefits: version control, repeatable deployments, easy rollback, and no manual console clicks. Think of it as a **blueprint for your AWS setup**.

---

## Q8: Compare RDS vs DynamoDB

| Feature | RDS | DynamoDB |
|---------|-----|----------|
| **Type** | Relational (SQL) | NoSQL (Key-Value) |
| **Scaling** | Vertical (bigger instance) | Horizontal (auto-scale) |
| **Schema** | Fixed schema | Flexible schema |
| **Best For** | Complex queries, joins | High-speed, simple lookups |
| **Operations** | More management | Fully managed |

> **Answer:** RDS is for relational data with complex queries. DynamoDB is for high-performance, serverless NoSQL at any scale.

---

## Q9: What are IAM best practices?

> **Answer:**
> 1. Enable MFA for all users
> 2. Never use root account
> 3. Follow least privilege principle
> 4. Use IAM roles for EC2/Lambda
> 5. Rotate access keys regularly
> 6. Use groups to manage permissions
> 7. Audit with IAM Access Analyzer

---

## Q10: What is the Shared Responsibility Model?

> **Answer:** AWS is responsible for **security OF the cloud** (hardware, networking, facilities). You are responsible for security **IN the cloud** (data, OS patching, firewall rules, encryption). It's a shared job — AWS handles the foundation, you handle what you build on top.

---

## 📝 Summary

| Topic | Key Takeaway |
|-------|-------------|
| **Security** | Defense in depth — multiple layers, never trust one control |
| **Encryption** | At rest AND in transit, use KMS |
| **IAM** | Least privilege, MFA, no root |
| **Cost** | Right-size, use RIs/Spot, delete waste |
| **Interviews** | Know comparisons (S3/EBS/EFS, Lambda/EC2, RDS/DynamoDB) |

> **Ravi:** "I feel like I could actually talk about this in an interview now!"
>
> **Rithu:** "You absolutely can! Now let's deep-dive into KMS — the encryption engine behind all of this! 🚀"

---

## ➡️ Next: [24 - KMS (Key Management Service)](../24%20-%20KMS%20-%20Key%20Management%20Service/README.md)
