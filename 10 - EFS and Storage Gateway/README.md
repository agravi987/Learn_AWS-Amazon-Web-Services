# 10 - EFS (Elastic File System) & Storage Gateway

![Phase-3-Storage](https://img.shields.io/badge/Phase-3-Storage-green) ![Difficulty-Medium](https://img.shields.io/badge/Difficulty-⭐⭐-Medium-yellow) ![ReadTime](https://img.shields.io/badge/Read%20Time-~10%20min-blue)

> *"Ravi, what if multiple servers need to read and write the SAME files at the SAME time?"*
> *"Hmm... plug in a shared USB drive?"*
> *"Close! But in the cloud, we do it better — with EFS!"* 🎉

---

## EFS Architecture Overview

![EFS Architecture](http://docs.aws.amazon.com/efs/latest/ug/images/overview-flow.png)

> *Multiple EC2 instances can mount and access the same EFS file system simultaneously.*

---

## What is EFS?

**EFS (Elastic File System)** is AWS's fully managed **Network File System (NFS)**. It provides a shared file system that **multiple EC2 instances can mount simultaneously**.

Unlike EBS (which attaches to ONE instance), EFS is designed for **concurrent access from many instances**.

### Key Difference

| Feature | EBS | EFS |
|---------|-----|-----|
| Access | One EC2 at a time | Multiple EC2s simultaneously |
| Type | Block Storage | File Storage |
| Analogy | USB drive | Shared Google Drive |
| Management | You manage volumes | Fully serverless |

---

## Why Do We Need EFS?

Ravi, imagine a **web application** running on 5 EC2 instances behind a load balancer. All 5 need access to the **same product images, config files, and user uploads**.

With EBS, you'd need to:
- Copy files to all 5 volumes separately 😩
- Sync them constantly 🤯
- Deal with conflicts when two servers write the same file 😱

With EFS, all 5 instances **mount the same file system** and see the **same files instantly**. Problem solved! ✅

---

## Real-World Analogy

Think of EFS like a **shared Google Drive folder**:

- 📂 **EBS** = Your personal USB drive — only YOU can access it
- 📂 **EFS** = A shared Google Drive — multiple team members can open, edit, and save files simultaneously
- No one needs to carry anything — it's always available over the network

---

## How EFS Works

```
EC2 Instance A ──┐
                  ├──► EFS File System (NFS v4.1) ──► Data stored across multiple AZs
EC2 Instance B ──┘
```

### The Magic Behind EFS

1. **Create an EFS file system** in the AWS Console
2. **Create mount targets** in your VPC subnets
3. **Mount the EFS** on your EC2 instances using the NFS client
4. **All instances see the same files** — reads and writes happen in real-time

---

## Key Features of EFS

| Feature | Description |
|---------|-------------|
| **Serverless** | No servers to provision or manage |
| **Elastic** | Automatically grows and shrinks as you add/remove files |
| **Multi-AZ** | Data is stored across multiple AZs by default |
| **NFS v4.0/v4.1** | Industry-standard protocol |
| **Performance Modes** | General Purpose or Max I/O |
| **Throughput Modes** | Bursting or Provisioned |
| **Storage Classes** | Standard, Infrequent Access (IA), Archive |
| **Encryption** | At rest (KMS) and in transit (TLS) |

---

## EFS Performance Modes

| Mode | Best For | When to Use |
|------|----------|-------------|
| **General Purpose** | Most workloads | Default choice for most apps |
| **Max I/O** | Parallel processing | Large-scale analytics, big data |

---

## Real-World Use Cases for EFS

- 🌐 **Web Content Serving** — Multiple web servers share the same HTML/CSS/JS files
- 📁 **Shared Configuration** — Multiple app instances read the same config files
- 🖼️ **Content Management Systems** — CMS instances share uploaded media
- 📊 **Data Analytics** — Multiple compute nodes process the same dataset
- 🔄 **Container Storage** — EKS/ECS tasks share persistent data via EFS

---

## What is Storage Gateway?

**AWS Storage Gateway** is a hybrid cloud storage service. It **bridges your on-premises storage with AWS cloud storage**.

Think of it as a **translator** between your local servers and AWS S3.

---

## Storage Gateway Types

| Gateway Type | What It Does | Uses |
|-------------|--------------|------|
| **File Gateway** | Exposes S3 as NFS/SMB file shares | On-prem apps accessing S3 |
| **Volume Gateway (Cached)** | Stores full copies locally, syncs to S3 | Disaster recovery, backup |
| **Volume Gateway (Stored)** | Stores full data locally, async to S3 | Low-latency access needed |
| **Tape Gateway** | Virtual tape library in S3 | Replaces physical tape drives |

---

## Real-World Analogy for Storage Gateway

Ravi, imagine you have a **filing cabinet** at your office (on-premises). You want to also have a copy of everything in the **cloud** without changing how your employees file papers.

Storage Gateway is like a **magic portal** installed on the cabinet door — employees keep filing papers the same way, but everything **automatically syncs to the cloud** behind the scenes! 🪄

---

## Architecture Overview

```
On-Premises Data Center
┌──────────────────────┐
│   App Servers        │
│   ┌──────────────┐   │
│   │ Storage      │   │
│   │ Gateway      │───┼────► AWS Cloud
│   │ (VM/Image)   │   │      ┌─────────────┐
│   └──────────────┘   │      │ S3 Bucket    │
│   Physical File Server│      │ EBS Snapshots│
└──────────────────────┘      │ EFS          │
                               └─────────────┘
```

---

## Best Practices for EFS & Storage Gateway

### EFS
- ✅ Use **EFS IA (Infrequent Access)** lifecycle policy to save costs
- ✅ Enable **encryption at rest** with KMS
- ✅ Use **security groups** to control NFS access
- ✅ Place mount targets in **private subnets** for security
- ✅ Use **access points** for permission management

### Storage Gateway
- ✅ Use **File Gateway** for most on-prem to S3 use cases
- ✅ Ensure **adequate local cache** for Volume Gateway
- ✅ Monitor gateway health via **CloudWatch**
- ✅ Use **Tape Gateway** to replace expensive physical tape infrastructure

---

## Common Mistakes

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Not enabling lifecycle policies | You pay for full-price storage you don't need | Set up EFS IA lifecycle |
| Exposing EFS publicly | Security risk — NFS isn't designed for public access | Use security groups + private subnets |
| Forgetting mount targets | Instances can't connect without them | Create mount targets in each AZ |
| Using EFS for single-instance apps | EBS is cheaper and faster for single-instance use | Use EBS when you only need one EC2 |
| Skipping Storage Gateway planning | Leads to poor performance | Plan cache sizing and network bandwidth |

---

## Interview Questions

### Q1: What's the difference between EBS and EFS?
**Answer:** EBS is block storage attached to a **single** EC2 instance. EFS is file storage (NFS) accessible by **multiple** EC2 instances simultaneously. EBS is like a USB drive; EFS is like a shared network drive.

### Q2: How does EFS handle high availability?
**Answer:** EFS automatically stores data across **multiple AZs** within a region. If one AZ goes down, your data is still accessible from other AZs. No manual replication needed.

### Q3: When would you use Storage Gateway instead of direct S3 access?
**Answer:** When on-premises applications need to access S3 using **standard protocols** (NFS/SMB) without code changes. Storage Gateway acts as a bridge, letting legacy apps use cloud storage transparently.

### Q4: Can you use EFS with containers?
**Answer:** Yes! EFS integrates with **ECS and EKS** for persistent shared storage across containers. It's perfect for stateful container workloads.

### Q5: How can you reduce EFS costs?
**Answer:** Enable **lifecycle policies** to automatically move infrequently accessed files to EFS IA (cheaper tier), enable **encryption** (no extra cost), and right-size your **throughput mode**.

---

## Summary

| Concept | Key Takeaway |
|---------|-------------|
| **EFS** | Shared NFS file system for multiple EC2 instances |
| **EFS Serverless** | No management — just create and mount |
| **EFS Multi-AZ** | Data automatically spread across AZs |
| **Storage Gateway** | Hybrid bridge between on-prem and AWS cloud |
| **File Gateway** | Exposes S3 as NFS/SMB |
| **Volume Gateway** | Block storage backed by S3 |
| **Tape Gateway** | Virtual tape library in S3 |

---

> 📝 **Rithu says:** *"Ravi, EFS gives your servers a shared brain. Storage Gateway gives your on-prem data a bridge to the cloud. Both make life easier!"*

---

**Next:** [11 - VPC (Virtual Private Cloud)](../11%20-%20VPC%20-%20Virtual%20Private%20Cloud/README.md) → Let's learn about AWS networking! 🌐
