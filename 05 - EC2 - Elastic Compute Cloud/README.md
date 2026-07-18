# 05 — EC2 (Elastic Compute Cloud)

![Phase](https://img.shields.io/badge/Phase-2%20Compute-purple?style=flat-square)
![Difficulty](https://img.shields.io/badge/Difficulty-⭐⭐%20Medium-yellow?style=flat-square)
![Read%20Time](https://img.shields.io/badge/Read%20Time-~15%20min-yellow?style=flat-square)

> *"Ravi, EC2 is the service that started it all for AWS. It's a virtual server you can rent in minutes. Need a computer? Spin one up. Done with it? Terminate. No paperwork, no hardware, just code."* — **Rithu** 💻

---

## What is EC2?

EC2 (Elastic Compute Cloud) is a service that provides **virtual servers** (called instances) in the cloud. You choose the specs, the operating system, and the region — and AWS runs it for you.

---

## Why Do We Need It?

- **No hardware to buy** — get a server in 2 minutes
- **Scale up or down** — need more power? Resize it
- **Pay per use** — stop it when you don't need it
- **Full control** — install any software, run any OS

---

## Real-World Analogy

> EC2 is like **renting a flat:**
>
> - **Instance Type** = Size of flat (1BHK, 2BHK, 3BHK)
> - **AMI** = The furniture and setup inside
> - **Security Group** = Locks on the door (who can enter)
> - **Key Pair** = Your house key (for SSH access)
> - **EBS Volume** = The hard disk inside (for storing your stuff)
> - **Public IP** = Your address (so people can find you)

---

## EC2 Architecture Overview

![EC2 Instance Types](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/instance-types.png)

> *Each EC2 instance type provides a balance of compute, memory, network, and storage resources.*

---

## How It Works

```
  You (via Console/CLI)
       │
       │ Launch Instance
       ▼
  AWS creates virtual machine in data center
       │
       ├── OS: Amazon Linux 2
       ├── CPU: 2 vCPUs
       ├── RAM: 4 GB
       ├── Storage: 30 GB EBS
       ├── Network: VPC + Subnet
       └── Firewall: Security Group
       │
       ▼
  You get: ec2-user@3.15.20.100
       │
       ▼
  SSH in → Install software → Run your app 🚀
```

---

## Key Concepts

### Instance Types

| Family | Use Case | Example |
|:-------|:---------|:--------|
| **T-series** (Burstable) | General purpose, dev/test | t2.micro, t3.medium |
| **M-series** (General) | Balanced compute/memory | m5.large |
| **C-series** (Compute) | CPU-intensive workloads | c5.xlarge |
| **R-series** (Memory) | Databases, in-memory caching | r5.large |
| **G-series** (GPU) | Machine learning, video processing | g4dn.xlarge |

> *Rithu: "Start with t2.micro for learning — it's Free Tier eligible. Don't spin up a GPU instance for your Hello World website."* 🙃

### Instance States

| State | What It Means |
|:------|:-------------|
| **Running** | Server is on, you're paying |
| **Stopped** | Server is off, you're NOT paying for compute (but EBS storage still costs) |
| **Terminated** | Server is deleted, gone forever |
| **Pending** | Starting up |
| **Shutting Down** | Powering off |

### Key Pairs

| Aspect | Detail |
|:-------|:-------|
| **What** | SSH key pair for secure login |
| **Public Key** | Stored on AWS (attached to instance) |
| **Private Key** | You keep it (.pem file) |
| **Use** | `ssh -i key.pem ec2-user@<IP>` |

> ⚠️ **Never share your .pem file!** It's like giving someone your house key.

### Security Groups

| Aspect | Detail |
|:-------|:-------|
| **What** | Virtual firewall for your instance |
| **Inbound** | Who can connect TO your instance |
| **Outbound** | Where your instance can connect |
| **Stateful** | If you allow inbound, response is automatically allowed outbound |

**Common Rules:**

| Type | Port | Source | Purpose |
|:-----|:-----|:-------|:--------|
| SSH | 22 | Your IP only | Admin access |
| HTTP | 80 | 0.0.0.0/0 | Web traffic |
| HTTPS | 443 | 0.0.0.0/0 | Secure web traffic |

---

## Architecture Overview

```
  ┌─────────────────────────────────────────────┐
  │                  AWS Cloud                   │
  │                                             │
  │  ┌─────────────────────────────────────┐    │
  │  │           VPC (Your Network)         │    │
  │  │                                     │    │
  │  │  ┌──────────────────────────────┐   │    │
  │  │  │      Public Subnet            │   │    │
  │  │  │  ┌────────────────────────┐  │   │    │
  │  │  │  │     EC2 Instance        │  │   │    │
  │  │  │  │  ┌──────────────────┐  │  │   │    │
  │  │  │  │  │ Security Group   │  │  │   │    │
  │  │  │  │  │ (Port 22, 80)    │  │  │   │    │
  │  │  │  │  └──────────────────┘  │  │   │    │
  │  │  │  │  ┌──────────────────┐  │  │   │    │
  │  │  │  │  │ EBS Volume       │  │  │   │    │
  │  │  │  │  │ (30 GB storage)  │  │  │   │    │
  │  │  │  │  └──────────────────┘  │  │   │    │
  │  │  │  └────────────────────────┘  │   │    │
  │  │  └──────────────────────────────┘   │    │
  │  └─────────────────────────────────────┘    │
  └─────────────────────────────────────────────┘
```

---

## Common Use Cases

- **Web hosting** — Run Apache/Nginx on EC2
- **Application servers** — Host your backend code
- **Development environments** — Spin up dev servers on demand
- **CI/CD runners** — Run Jenkins, GitHub Actions runners
- **Batch processing** — Process large datasets

---

## Best Practices

1. **Use IAM Roles** instead of access keys on EC2
2. **Stop instances** when not in use (saves compute cost)
3. **Use Security Groups** — never open Port 22 to 0.0.0.0/0
4. **Use Elastic IP** if you need a static public IP
5. **Tag everything** — Name, Environment, Owner
6. **Use Auto Scaling** instead of a single large instance
7. **Choose the right instance type** — don't oversize

---

## Common Mistakes

| Mistake | Why It's Bad | Fix |
|:--------|:------------|:----|
| Opening SSH to 0.0.0.0/0 | Anyone can brute-force your password | Limit to your IP |
| Using root account to launch | No audit trail | Use IAM user |
| Forgetting to stop terminated instances | They don't exist after termination | Can't stop what's terminated |
| Not tagging resources | Can't identify what costs what | Always tag |
| Leaving instances running 24/7 | Expensive! | Stop when not in use |

---

## Interview Questions

1. **What is EC2?**
   > Elastic Compute Cloud — provides virtual servers (instances) in AWS cloud on-demand.

2. **What is the difference between Stop and Terminate?**
   > Stop = server off, data preserved on EBS. Terminate = server deleted, data lost (unless delete-on-termination is disabled).

3. **What is a Security Group?**
   > A virtual firewall that controls inbound and outbound traffic to an EC2 instance. It's stateful.

4. **What is an AMI?**
   > Amazon Machine Image — a template with OS, software, and configuration used to launch EC2 instances.

5. **What instance type should I use for Free Tier?**
   > t2.micro — eligible for 750 hours/month free for 12 months.

---

## Summary

| Concept | Key Point |
|:--------|:----------|
| **EC2** | Virtual server in the cloud |
| **Instance Types** | Choose based on workload (CPU, RAM, GPU) |
| **Security Group** | Virtual firewall — control who connects |
| **Key Pair** | SSH key for secure login |
| **Free Tier** | t2.micro — 750 hours/month |
| **Rule #1** | Stop unused instances, tag everything |

---

**Next:** [06 — EBS and AMI](../06%20-%20EBS%20and%20AMI/) → *Your EC2's hard drive and snapshot*

---

> *"Ravi, EC2 is like Netflix for servers. You pick what you want, press play, and it just works. Except instead of paying ₹200/month, you pay by the second."* — **Rithu** 🍿
