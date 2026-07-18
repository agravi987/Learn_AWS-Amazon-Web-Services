# 06 - EBS (Elastic Block Store) & AMI (Amazon Machine Image)

![Phase-2 Compute](https://img.shields.io/badge/Phase-2%20Compute-purple)
![Difficulty](https://img.shields.io/badge/Difficulty-%E2%AD%90%E2%AD%90%20Medium-yellow)
![Read Time](https://img.shields.io/badge/Read%20Time-~12%20min-blue)

---

## EBS Architecture Overview

![EBS Volume Architecture](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ebs-architecture.png)

> *EBS volumes are network-attached storage that persist independently of your EC2 instance.*

---

## What is EBS?

Hey Ravi! Ever wished your EC2 instance had its own hard drive that sticks around even after you reboot? That's exactly what **EBS (Elastic Block Store)** is — a virtual hard drive in the cloud that persists data independently of your EC2 instance.

Think of EC2 as a computer and EBS as its external hard disk. You can attach, detach, and reattach EBS volumes to different instances like swapping a USB drive between laptops.

---

## Why Do We Need EBS?

Without EBS, EC2 instances would use **Instance Store** — which is ephemeral. That means:

- 🚨 Data is LOST when the instance stops or terminates
- 🚨 No way to take backups easily
- 🚨 No flexibility to resize or move storage

EBS solves all of this:

| Problem | EBS Solution |
|---------|-------------|
| Data loss on stop | Persistent storage |
| Can't backup easily | EBS Snapshots |
| Fixed storage size | Resize volumes anytime |
| Need different performance | Multiple volume types |

---

## Real-World Analogy

Ravi, imagine two scenarios:

**Instance Store** = Writing on a whiteboard 📝
- Fast to write, but everything disappears when you erase it (terminate instance)

**EBS** = Writing in a notebook 📓
- Sits on your desk (attached to instance)
- You can take it to another desk (reattach to another instance)
- You can photocopy pages (snapshots) for backup
- Even if your desk catches fire, your notebook is safe!

---

## How EBS Works

When you create an EBS volume:
1. You pick a **volume type** (performance tier)
2. You pick a **size** (1 GB to 64 TB)
3. You pick an **AZ** (must be in the same AZ as your EC2)
4. You **attach** it to your EC2 instance
5. The OS sees it as a local block device (like `/dev/xvdf`)

---

## EBS Volume Types

This is where people get confused, Ravi. Let me break it down simply:

| Volume Type | Use Case | Max IOPS | Max Throughput | Cost |
|------------|----------|----------|----------------|------|
| **gp3** (General SSD) | Most workloads, boot volumes | 16,000 | 1,000 MB/s | 💰 Cheap |
| **io1/io2** (Provisioned SSD) | Databases, high-performance apps | 64,000 | 1,000 MB/s | 💰💰💰 Expensive |
| **st1** (Throughput HDD) | Big data, data warehouses, logs | 500 | 500 MB/s | 💰💰 Moderate |
| **sc1** (Cold HDD) | Infrequent access, archives | 250 | 250 MB/s | 💰 Cheapest |

> **Pro Tip for Ravi:** Start with **gp3**. It's the sweet spot for 90% of use cases — better performance and cheaper than the older gp2!

---

## EBS Snapshots = Your Backup Plan

A **Snapshot** is a point-in-time backup of your EBS volume stored in S3.

Key things to know:

- ✅ **Incremental** — only saves changed blocks since last snapshot (saves money!)
- ✅ **Region-specific** — but you can copy snapshots across regions
- ✅ **Can create AMI from snapshots** (more on this below)
- ✅ **Can restore a snapshot to a new volume** anytime

**Best Practice:** Automate snapshots using **AWS Data Lifecycle Manager** on a schedule (daily, weekly, etc.)

---

## What is AMI?

Now let's talk about **AMI (Amazon Machine Image)**. Think of AMI as a **complete template** for launching an EC2 instance.

An AMI includes:

| Component | Description |
|-----------|-------------|
| Operating System | Linux, Windows, etc. |
| Application Server | Apache, Nginx, etc. |
| Applications | Your custom app code |
| EBS Snapshots | All attached volume data |

### AMI vs EBS Snapshot — What's the Difference?

| Feature | EBS Snapshot | AMI |
|---------|-------------|-----|
| **What it covers** | Single volume | Entire instance |
| **Purpose** | Backup a volume | Launch new instances |
| **Includes** | Data on one volume | OS + Apps + Multiple volumes |
| **Use case** | Restore data | Replicate entire server |

> **Analogy:** A snapshot is like photocopying one notebook. An AMI is like photocopying your entire desk — computer, books, sticky notes, and all!

### How to Create an AMI

1. Launch an EC2 instance with your desired setup
2. Install your apps, configure everything
3. Go to **EC2 → Actions → Image → Create Image**
4. AWS creates EBS snapshots behind the scenes
5. Now you can launch identical instances from this AMI anytime!

---

## EBS vs Instance Store

| Feature | EBS | Instance Store |
|---------|-----|----------------|
| **Persistence** | Persistent ✅ | Ephemeral ❌ |
| **Performance** | Good to Excellent | Very Fast (physical disk) |
| **Size** | Up to 64 TB | Limited by instance type |
| **Backup** | Snapshots ✅ | Manual ❌ |
| **Detach & Reattach** | Yes ✅ | No ❌ |
| **Use Case** | Most workloads | Temporary caching, scratch space |

> **Ravi's Rule of Thumb:** If losing data would make you cry, use EBS! 😄

---

## Delete on Termination

This is a critical setting, Ravi!

When you **terminate** an EC2 instance, what happens to the root EBS volume?

- **Default for Root Volume:** Depends on the AMI (some delete, some keep)
- **Default for Additional Volumes:** Never deleted (safe by default)

**To change this behavior:**

1. Go to EC2 → Instance → Actions → Storage → Manage Block Device Mappings
2. Or check the box **"Delete on Termination"** when launching an instance

> ⚠️ **Warning:** If you forget to set "Delete on Termination = No" for important volumes, you could lose data when the instance is terminated!

---

## Architecture Overview

Here's how EBS and EC2 work together:

```
┌──────────────────────────────────────────────────────┐
│                    AWS Cloud                          │
│                                                      │
│  ┌─────────────────┐       ┌──────────────────────┐ │
│  │    EC2 Instance  │◄─────│   EBS Volume (gp3)   │ │
│  │                  │  Attach│  100 GB SSD          │ │
│  │  ┌────────────┐ │       └──────────────────────┘ │
│  │  │   OS       │ │                                  │
│  │  │   Apps     │ │       ┌──────────────────────┐ │
│  │  │   Data     │ │       │  EBS Snapshot (S3)   │ │
│  │  └────────────┘ │       │  Point-in-time backup│ │
│  └─────────────────┘       └──────────────────────┘ │
│         │                        ▲                    │
│         │   Launch from AMI      │   Create Snapshot   │
│         └────────────────────────┘                    │
└──────────────────────────────────────────────────────┘
```

---

## Common Use Cases

| Use Case | Volume Type | Why |
|----------|-------------|-----|
| Boot volumes | gp3 | Fast boot, good performance |
| Database (SQL/NoSQL) | io2 | High IOPS needed |
| Data warehouse | st1 | High throughput, sequential reads |
| Archival / rarely accessed | sc1 | Cheapest option |
| Development/testing | gp3 | Affordable, flexible |

---

## Best Practices

1. 📸 **Take snapshots regularly** — Automate with Lifecycle Manager
2. 🏷️ **Tag everything** — Name your volumes, track costs
3. ⚡ **Choose the right volume type** — Don't overpay for io2 when gp3 works fine
4. 🔒 **Enable encryption** — Use AWS KMS for data at rest
5. 🗑️ **Set Delete on Termination correctly** — Protect production data
6. 📈 **Monitor with CloudWatch** — Watch IOPS and throughput metrics
7. 🔄 **Use gp3 over gp2** — Better price-performance ratio

---

## Common Mistakes

| Mistake | Impact | Fix |
|---------|--------|-----|
| Not taking snapshots | Data loss risk | Automate snapshots |
| Using io2 for simple apps | Overpaying 💸 | Use gp3 unless high IOPS needed |
| Forgetting Instance Store is ephemeral | Data loss on stop/terminate | Use EBS for persistent data |
| Wrong AZ for EBS volume | Can't attach to EC2 | Always create in same AZ as instance |
| Not encrypting sensitive data | Security risk | Enable KMS encryption |
| Ignoring Delete on Termination | Lost production volumes | Set to "No" for important data |

---

## Interview Questions

### Q1: What is the difference between EBS and Instance Store?
**Answer:** EBS is persistent network-attached storage — data survives instance stops. Instance Store is physically attached to the host — data is lost when the instance stops. Use EBS for anything important; use Instance Store for temporary/cache data.

### Q2: What happens to EBS data when you stop an EC2 instance?
**Answer:** Nothing! EBS data persists when an instance is **stopped**. Data is only at risk when the instance is **terminated** (unless Delete on Termination is set to No).

### Q3: What is an EBS Snapshot and how is it different from an AMI?
**Answer:** A snapshot is a backup of a single EBS volume stored in S3 (incremental). An AMI is a template for an entire instance (OS + apps + multiple volumes). Snapshots are used to restore volumes; AMIs are used to launch new instances.

### Q4: Can you attach an EBS volume in us-east-1a to an EC2 instance in us-east-1b?
**Answer:** No! EBS volumes must be in the **same Availability Zone** as the EC2 instance. You'd need to create a snapshot and restore it in the target AZ.

### Q5: Which EBS volume type should you choose for a production database?
**Answer:** **io2** (or io1) for databases requiring high IOPS. For less demanding databases, **gp3** with provisioned IOPS can work well. The choice depends on your IOPS and throughput requirements.

---

## Summary

| Concept | Key Takeaway |
|---------|-------------|
| **EBS** | Virtual persistent hard drive for EC2 |
| **Snapshots** | Incremental backups stored in S3 |
| **AMI** | Full instance template (OS + Apps + Data) |
| **gp3** | Go-to volume type for most workloads |
| **Delete on Termination** | Set carefully to protect data |
| **Instance Store** | Fast but ephemeral — use wisely |

---

**Next:** [07 - Auto Scaling Group](../07%20-%20Auto%20Scaling%20Group/README.md) → Learn how AWS automatically scales your EC2 fleet based on demand! 🚀
