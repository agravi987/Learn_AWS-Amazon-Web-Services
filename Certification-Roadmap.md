# 🎓 AWS Certification Roadmap

> *"Ravi, these notes aren't just for learning — they're your certification prep. Here's how they map to actual AWS exams."* — **Rithu** 📜

---

## 🗺️ Which Certification Should You Get First?

```
                    ┌─────────────────────┐
                    │  Cloud Practitioner  │ ← Start here (entry-level)
                    │     (CLF-C02)        │
                    └──────────┬──────────┘
                               │
              ┌────────────────┴────────────────┐
              ▼                                 ▼
  ┌───────────────────┐             ┌───────────────────┐
  │ Solutions Architect│             │  SysOps Admin     │
  │   Associate (SAA) │             │   Associate (SOA) │
  └─────────┬─────────┘             └─────────┬─────────┘
            │                                 │
            ▼                                 ▼
  ┌───────────────────┐             ┌───────────────────┐
  │ Solutions Architect│             │  DevOps Engineer  │
  │  Professional (SAP)│             │  Professional(DOP)│
  └───────────────────┘             └───────────────────┘
```

**Recommended Path:** Cloud Practitioner → Solutions Architect Associate → DevOps Engineer Professional

---

## 📊 Topic-to-Certification Mapping

| # | Topic | Cloud Practitioner | SAA | SOA | DevOps Pro |
|:-:|:------|:------------------:|:---:|:---:|:----------:|
| 00 | Cloud Computing | ✅ | ✅ | | |
| 01 | Global Infrastructure | ✅ | ✅ | | |
| 02 | Well-Architected Framework | ✅ | ✅ | ✅ | ✅ |
| 03 | IAM | ✅ | ✅ | ✅ | ✅ |
| 04 | AWS CLI | | ✅ | ✅ | ✅ |
| 05 | EC2 | ✅ | ✅ | ✅ | ✅ |
| 06 | EBS and AMI | ✅ | ✅ | ✅ | ✅ |
| 07 | Auto Scaling Group | | ✅ | ✅ | ✅ |
| 08 | Elastic Load Balancer | | ✅ | ✅ | ✅ |
| 09 | S3 | ✅ | ✅ | ✅ | ✅ |
| 10 | EFS and Storage Gateway | | ✅ | ✅ | |
| 11 | VPC | | ✅ | ✅ | ✅ |
| 12 | Route 53 | | ✅ | ✅ | ✅ |
| 13 | RDS | ✅ | ✅ | ✅ | ✅ |
| 14 | DynamoDB | | ✅ | ✅ | |
| 15 | CloudWatch | | ✅ | ✅ | ✅ |
| 16 | CloudTrail | ✅ | ✅ | ✅ | ✅ |
| 17 | SSM, Secrets Manager, Parameter Store | | ✅ | ✅ | ✅ |
| 18 | SNS, SQS, EventBridge | | ✅ | ✅ | ✅ |
| 19 | Lambda and API Gateway | | ✅ | ✅ | ✅ |
| 20 | ECS, ECR and EKS | | ✅ | ✅ | ✅ |
| 21 | CloudFormation | | ✅ | ✅ | ✅ |
| 22 | AWS Organizations | ✅ | ✅ | | |
| 23 | Security, Cost & Interview Q&A | ✅ | ✅ | ✅ | ✅ |
| 24 | KMS | | ✅ | ✅ | ✅ |
| 25 | AWS Backup | | ✅ | ✅ | |

---

## 🎯 Certification Breakdown

### ☁️ Cloud Practitioner (CLF-C02)
> *"The basics — prove you know what cloud IS."*

| Area | Weight | Topics to Focus |
|:-----|:-------|:----------------|
| Cloud Concepts | 24% | 00, 01, 02 |
| Security & Compliance | 30% | 03, 23, 24 |
| Cloud Technology & Services | 34% | 05, 06, 09, 13, 19 |
| Billing & Pricing | 12% | 23 (cost section) |

**Topics from this repo:** 00, 01, 02, 03, 05, 06, 09, 13, 16, 19, 22, 23, 24

---

### 🏗️ Solutions Architect Associate (SAA-C03)
> *"Design resilient, high-performing, secure architectures."*

| Area | Weight | Topics to Focus |
|:-----|:-------|:----------------|
| Resilient Architectures | 26% | 07, 08, 11, 13 |
| High-Performing Architectures | 26% | 05, 06, 09, 14, 19 |
| Secure Architectures | 24% | 03, 11, 24 |
| Cost-Optimized Architectures | 24% | 09, 23, 25 |

**Topics from this repo:** ALL topics (00-25)

---

### ⚙️ SysOps Administrator Associate (SOA-C03)
> *"Deploy, manage, and operate on AWS."*

| Area | Weight | Topics to Focus |
|:-----|:-------|:----------------|
| Monitoring, Logging & Remediation | 20% | 15, 16, 17 |
| Reliability & Business Continuity | 16% | 07, 08, 13, 25 |
| Deployment, Provisioning & Automation | 18% | 04, 21, 19 |
| Security & Compliance | 16% | 03, 17, 24 |
| Networking & Content Delivery | 18% | 11, 12, 08 |
| Cost & Performance Optimization | 12% | 23 |

**Topics from this repo:** ALL topics (00-25)

---

### 🚀 DevOps Engineer Professional (DOP-C02)
> *"The ultimate DevOps cert — CI/CD, IaC, monitoring, and more."*

| Area | Weight | Topics to Focus |
|:-----|:-------|:----------------|
| SDLC Automation | 22% | 21, 04, 19 |
| Configuration Management & IaC | 17% | 21, 17, 04 |
| Resilient Cloud Solutions | 15% | 07, 08, 13, 25 |
| Monitoring & Logging | 15% | 15, 16 |
| Security & Governance | 12% | 03, 22, 24, 23 |
| Incident & Event Response | 9% | 15, 16, 18 |
| High Availability & Disaster Recovery | 10% | 07, 08, 11, 25 |

**Topics from this repo:** ALL topics (00-25)

---

## 📅 Study Plan

### For Cloud Practitioner (1-2 weeks):
1. Read topics 00-03, 05, 06, 09, 13, 23, 24
2. Take practice exams
3. Focus on billing and pricing concepts

### For Solutions Architect Associate (4-6 weeks):
1. Read ALL topics (00-25)
2. Take detailed notes on VPC, EC2, S3, RDS, Lambda
3. Practice scenario-based questions
4. Focus on "which service to use when"

### For DevOps Engineer Professional (6-8 weeks):
1. Master topics 04, 07, 08, 15, 16, 17, 21
2. Understand CI/CD pipelines deeply
3. Practice CloudFormation templates
4. Focus on automation and monitoring

---

> *"Ravi, the Solutions Architect Associate is the most valuable cert for getting hired. Start there after Cloud Practitioner."* — **Rithu** 🎯
