# ☁️ AWS Notes — Learn Cloud Computing & DevOps

> *"Hey Ravi! Welcome to your AWS notes. I'm Rithu — your cloud bestie who's going to walk you through every service, one concept at a time. No boring textbooks, no overwhelming docs. Just us, AWS, and a lot of 'oh THAT's how it works!' moments."* — **Rithu** 😄

---

## 🎯 What Is This Repository?

A **beginner-friendly, theory-focused** AWS learning repository designed for:

- 📚 **Personal learning** — understand AWS from zero
- 🎤 **Interview preparation** — each topic has interview questions
- 📝 **Certification revision** — concise summaries for quick review
- 🔮 **Future reference** — come back anytime you forget a concept

> This repo focuses on **concepts and understanding**, not hands-on labs. Labs will live in a separate repository.

---

## 🗺️ The Learning Path

> *Rithu says: "Don't jump around, Ravi. Each topic builds on the previous one. It's like a video game — you can't fight the boss before leveling up."* 🎮

### Phase 1 — Cloud Foundations ☁️
*Know the playground before you play*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 00 | [What is Cloud Computing?](./00%20-%20What%20is%20Cloud%20Computing/) | The big picture — what cloud is and why it exists |
| 01 | [AWS Global Infrastructure](./01%20-%20AWS%20Global%20Infrastructure/) | Regions, AZs, Edge Locations — where your stuff lives |
| 02 | [Well-Architected Framework](./02%20-%20AWS%20Well-Architected%20Framework/) | The 6 pillars of good architecture |
| 03 | [IAM](./03%20-%20IAM%20-%20Identity%20and%20Access%20Management/) | Security is Layer 0 — learn it first |
| 04 | [AWS CLI](./04%20-%20AWS%20CLI/) | How real engineers talk to AWS |

### Phase 2 — Compute 💻
*Running things in the cloud*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 05 | [EC2](./05%20-%20EC2%20-%20Elastic%20Compute%20Cloud/) | Virtual servers — the backbone of AWS |
| 06 | [EBS and AMI](./06%20-%20EBS%20and%20AMI/) | Hard drives + server snapshots |
| 07 | [Auto Scaling Group](./07%20-%20Auto%20Scaling%20Group/) | Handling traffic spikes automatically |
| 08 | [Elastic Load Balancer](./08%20-%20Elastic%20Load%20Balancer/) | Distributing traffic like a pro |

### Phase 3 — Storage 📦
*Keeping your data safe*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 09 | [S3](./09%20-%20S3%20-%20Simple%20Storage%20Service/) | Object storage — the most used AWS service |
| 10 | [EFS and Storage Gateway](./10%20-%20EFS%20and%20Storage%20Gateway/) | Shared file storage + hybrid cloud |

### Phase 4 — Networking 🌐
*Connecting everything together*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 11 | [VPC](./11%20-%20VPC%20-%20Virtual%20Private%20Cloud/) | Your private network in the cloud |
| 12 | [Route 53](./12%20-%20Route%2053%20-%20DNS/) | DNS — how the internet finds your app |

### Phase 5 — Databases 🗄️
*Storing structured data*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 13 | [RDS](./13%20-%20RDS%20-%20Relational%20Database%20Service/) | Managed SQL databases |
| 14 | [DynamoDB](./14%20-%20DynamoDB%20-%20NoSQL%20Database/) | Serverless NoSQL at any scale |

### Phase 6 — Monitoring & Security 🔍
*Knowing what's happening*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 15 | [CloudWatch](./15%20-%20CloudWatch/) | Metrics, alarms, logs — your eyes on the cloud |
| 16 | [CloudTrail](./16%20-%20CloudTrail/) | Audit trail — who did what and when |
| 17 | [SSM, Secrets Manager & Parameter Store](./17%20-%20Systems%20Manager%2C%20Secrets%20Manager%20and%20Parameter%20Store/) | Managing secrets and operations securely |

### Phase 7 — Messaging 📨
*Decoupling your architecture*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 18 | [SNS, SQS & EventBridge](./18%20-%20SNS%2C%20SQS%20and%20EventBridge/) | Notifications, queues, and event routing |

### Phase 8 — Serverless & Containers 🐳
*Modern deployment patterns*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 19 | [Lambda & API Gateway](./19%20-%20Lambda%20and%20API%20Gateway/) | Run code without servers |
| 20 | [ECS, ECR & EKS](./20%20-%20ECS%2C%20ECR%20and%20EKS/) | Containers — Docker on AWS |

### Phase 9 — Infrastructure as Code 📝
*Automating everything*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 21 | [CloudFormation](./21%20-%20CloudFormation/) | Define infrastructure in code |

### Phase 10 — Governance & Career 🏢
*The big picture*

| # | Topic | What You'll Learn |
|:-:|:------|:------------------|
| 22 | [AWS Organizations](./22%20-%20AWS%20Organizations/) | Multi-account management for enterprises |
| 23 | [Security, Cost & Interview Q&A](./23%20-%20Security%2C%20Cost%20Optimization%20and%20Interview%20QA/) | Best practices + interview prep |
| 24 | [KMS](./24%20-%20KMS%20-%20Key%20Management%20Service/) | Encryption — how it actually works |
| 25 | [AWS Backup](./25%20-%20AWS%20Backup/) | Centralized backup strategy |

---

## 🧠 How Everything Connects

```
  👤 User types yoursite.com
        │
        ▼
  🌐 Route 53 (DNS) ────────── "Where should I send this?"
        │
        ▼
  🚀 CloudFront (CDN) ──────── "Serve from nearest edge!"
        │
        ▼
  ⚖️ ELB (Load Balancer) ───── "Which server is free?"
        │
        ▼
  📦 ASG (Auto Scaling) ─────── "Need more servers? Got you."
        │
        ▼
  💻 EC2 (Compute) ──────────── "Running inside a VPC"
        │
        ├──→ 🗄️ RDS (SQL) ──────── "Structured data"
        ├──→ 📋 DynamoDB (NoSQL) ── "Flexible data"
        ├──→ 📦 S3 (Objects) ────── "Files & backups"
        └──→ 🔐 KMS ─────────────── "Encrypting everything"

  🔥 Meanwhile, Lambda:
     "Y'all need servers? Could not be me. 😎"

  📨 SNS/SQS:
     "I'll make sure services talk without direct connections."

  🔍 CloudWatch & CloudTrail:
     "We're watching everything. Always. 👀"
```

---

## 📚 Quick Reference Guides

> *Rithu: "These are your secret weapons for interviews and exams!"* 🗡️

| Guide | What It Is | When To Use |
|:------|:-----------|:------------|
| [⚡ Cheat Sheet](./Cheat-Sheet.md) | One-page revision of ALL services | 30 min before an interview or exam |
| [🔄 Services Comparison](./AWS-Services-Comparison.md) | S3 vs EBS vs EFS, RDS vs DynamoDB, etc. | When interviewers ask "X vs Y?" |
| [🎓 Certification Roadmap](./Certification-Roadmap.md) | Maps topics to AWS certifications | Planning your certification journey |

---

## 📊 Stats

| Metric | Count |
|:-------|:------|
| **Total Topics** | 26 |
| **Phases** | 10 |
| **Core Services Covered** | 25+ |
| **Interview Questions** | 130+ |
| **Comparison Guides** | 10+ service comparisons |
| **Time to Complete (est.)** | ~20 hours |

---

## 🏗️ Repository Structure

```
├── README.md                              ← You are here
├── Cheat-Sheet.md                         ← Quick revision
├── AWS-Services-Comparison.md             ← X vs Y comparisons
├── Certification-Roadmap.md               ← Cert prep guide
├── CONTRIBUTING.md                        ← How to contribute
├── LICENSE                                ← MIT License
│
├── 00 - What is Cloud Computing/          ← ☁️ Start here
├── 01 - AWS Global Infrastructure/
├── 02 - Well-Architected Framework/
├── 03 - IAM/
├── 04 - AWS CLI/
├── 05 - EC2/
├── 06 - EBS and AMI/
├── 07 - Auto Scaling Group/
├── 08 - Elastic Load Balancer/
├── 09 - S3/
├── 10 - EFS and Storage Gateway/
├── 11 - VPC/
├── 12 - Route 53/
├── 13 - RDS/
├── 14 - DynamoDB/
├── 15 - CloudWatch/
├── 16 - CloudTrail/
├── 17 - SSM, Secrets Manager & Parameter Store/
├── 18 - SNS, SQS and EventBridge/
├── 19 - Lambda and API Gateway/
├── 20 - ECS, ECR and EKS/
├── 21 - CloudFormation/
├── 22 - AWS Organizations/
├── 23 - Security, Cost & Interview Q&A/
├── 24 - KMS/
└── 25 - AWS Backup/
```

---

## 💡 How to Use This Repository

1. **Start from 00** and go in order — don't skip
2. **Read each README** like a story, not a textbook
3. **Check the interview questions** at the end of each topic
4. **Use the [Cheat Sheet](./Cheat-Sheet.md)** for quick revision before interviews
5. **Check [Service Comparisons](./AWS-Services-Comparison.md)** when interviewers ask "X vs Y?"
6. **Revisit topics** when you forget — that's normal
7. **Build the hands-on labs** in a separate repo after understanding concepts
8. **Check the [Certification Roadmap](./Certification-Roadmap.md)** when you're ready for AWS certs

---

## 🤝 About

**Author:** Ravi — Learning AWS one topic at a time ☁️🚀

**Co-Author (Imaginary Bestie):** Rithu — The one who makes sure Ravi's notes don't put people to sleep 😴➡️😄

---

> *"Ravi, 26 topics might seem like a lot. But you know what's a lot more? Explaining to your interviewer why you don't know what a VPC is. Start reading."* — **Rithu** 💪☁️
