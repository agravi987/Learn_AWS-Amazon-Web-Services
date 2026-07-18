# ⚡ AWS Quick Revision Cheat Sheet

> *"Ravi, this is your exam-day cheat sheet. Read this 30 minutes before any interview or certification exam."* — **Rithu** 📝

---

## 🌍 Global Infrastructure

| Component | Count | Purpose |
|:----------|:------|:--------|
| **Regions** | 33+ | Geographic areas |
| **AZs** | 3-6 per Region | Isolated data centers |
| **Edge Locations** | 200+ | CDN caching points |
| **Local Zones** | Extensions of Regions | Low-latency for specific cities |

**Choose Region by:** Latency → Compliance → Cost

---

## 🔐 IAM Cheat Sheet

| Concept | Remember |
|:--------|:---------|
| **Root Account** | Setup only. NEVER for daily work. |
| **Users** | One per person |
| **Groups** | Share permissions across users |
| **Roles** | Temporary access for services (no credentials) |
| **Policies** | JSON: Effect + Action + Resource |
| **MFA** | Always enable on root + admin users |
| **Least Privilege** | Give minimum permissions only |

---

## 💻 EC2 Cheat Sheet

| Concept | Remember |
|:--------|:---------|
| **Free Tier** | t2.micro (750 hrs/month) |
| **Key Pair** | SSH key (.pem file) — never share! |
| **Security Group** | Virtual firewall (stateful) |
| **Stop vs Terminate** | Stop = data preserved, Terminate = data deleted |
| **User Data** | Bootstrap script on launch |
| **IAM Role** | Use instead of access keys on EC2 |

---

## 💾 Storage Cheat Sheet

| Service | Type | Use When |
|:--------|:-----|:---------|
| **S3** | Object | Files, backups, static sites |
| **EBS** | Block | EC2 hard drive |
| **EFS** | File | Multiple EC2s share one folder |
| **Glacier** | Archive | Long-term backup, cheapest |

**S3 Storage Classes (cheapest → most expensive):**
`Deep Archive` → `Glacier` → `IA` → `Standard`

---

## 🌐 VPC Cheat Sheet

| Component | Remember |
|:----------|:---------|
| **VPC** | Your private network |
| **Public Subnet** | Has route to Internet Gateway |
| **Private Subnet** | No direct internet access |
| **Internet Gateway** | Connects VPC to internet |
| **NAT Gateway** | Lets private subnet access internet outbound only |
| **Security Group** | Instance-level firewall (stateful) |
| **NACL** | Subnet-level firewall (stateless) |

**Security Group vs NACL:**
- Security Group: ALLOW only, stateful, instance-level
- NACL: ALLOW + DENY, stateless, subnet-level

---

## 🌐 Route 53 Cheat Sheet

| Record Type | Maps To |
|:------------|:--------|
| **A** | Domain → IPv4 |
| **AAAA** | Domain → IPv6 |
| **CNAME** | Domain → Another domain |
| **Alias** | Domain → AWS resource (ALB, S3, CloudFront) |

**Routing Policies:** Simple, Weighted, Latency, Failover, Geolocation

---

## 📦 S3 Cheat Sheet

| Feature | Remember |
|:--------|:---------|
| **Bucket Policy** | JSON for public access |
| **Versioning** | Keeps old versions (enable it!) |
| **Lifecycle Policy** | Auto-move old data to cheaper storage |
| **Encryption** | SSE-S3, SSE-KMS, SSE-C |
| **Static Website Hosting** | Host HTML/CSS/JS without servers |

---

## 🗄️ RDS Cheat Sheet

| Feature | Remember |
|:--------|:---------|
| **Multi-AZ** | High availability (auto-failover) |
| **Read Replicas** | Scale read traffic |
| **Automated Backups** | 35 days retention |
| **Engines** | MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Aurora |

---

## 📋 DynamoDB Cheat Sheet

| Feature | Remember |
|:--------|:---------|
| **Primary Key** | Partition Key (+ optional Sort Key) |
| **On-Demand Mode** | Pay per request |
| **Provisioned Mode** | Reserve capacity (cheaper) |
| **DAX** | In-memory caching layer |
| **Streams** | Track changes for triggers |

---

## ⚡ Lambda Cheat Sheet

| Feature | Remember |
|:--------|:---------|
| **Max Timeout** | 15 minutes |
| **Languages** | Python, Node.js, Java, Go, .NET, Ruby |
| **Triggers** | S3, API Gateway, DynamoDB, SQS, SNS, CloudWatch |
| **Cold Start** | First invocation may be slow |
| **Pricing** | Per request + duration (1M free requests/month) |

---

## 🔍 Monitoring Cheat Sheet

| Service | Purpose |
|:--------|:--------|
| **CloudWatch** | Metrics, alarms, logs |
| **CloudTrail** | Audit trail (who did what) |
| **AWS Config** | Resource configuration tracking |

---

## 📨 Messaging Cheat Sheet

| Service | Pattern | Use When |
|:--------|:--------|:---------|
| **SNS** | Pub/Sub (push) | Alerts, fan-out |
| **SQS** | Queue (pull) | Decoupling, background jobs |
| **EventBridge** | Event router | Complex event routing |

---

## 🔐 KMS Cheat Sheet

| Feature | Remember |
|:--------|:---------|
| **CMK** | Customer Master Key (encrypts data keys) |
| **Envelope Encryption** | CMK encrypts data key, data key encrypts data |
| **AWS-Managed Keys** | Free |
| **Customer-Managed Keys** | $1/month + API calls |
| **Integrates With** | S3, EBS, RDS, Lambda, Secrets Manager, CloudWatch |

---

## 🔑 Top 10 Interview One-Liners

1. **VPC** = Your private network with public/private subnets
2. **Security Group** = Instance-level stateful firewall
3. **NACL** = Subnet-level stateless firewall
4. **S3** = Object storage with unlimited scalability
5. **RDS** = Managed relational database with Multi-AZ
6. **Lambda** = Serverless compute, max 15 min, auto-scales
7. **ASG** = Auto-scales EC2 based on demand
8. **ELB** = Distributes traffic across instances
9. **CloudFormation** = Infrastructure as Code for AWS
10. **IAM** = Who can do what on which resources

---

> *"Ravi, if you remember these tables, you can answer 80% of AWS interview questions. The other 20% is just elaborating on these concepts."* — **Rithu** 💪
