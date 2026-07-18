# 11 - VPC (Virtual Private Cloud)

![Phase-4-Networking](https://img.shields.io/badge/Phase-4-Networking-orange) ![Difficulty-Hard](https://img.shields.io/badge/Difficulty-⭐⭐⭐-Hard-red) ![ReadTime](https://img.shields.io/badge/Read%20Time-~18%20min-blue)

> *"Ravi, how does AWS know which servers can talk to each other, and which can't?"*
> *"Umm... magic?"*
> *"Close! It's VPC — your own private network in the cloud!"* 🏗️

---

## What is VPC?

**VPC (Virtual Private Cloud)** is your **own isolated network** inside AWS. You get full control over:

- 🌐 **IP address ranges**
- 🏠 **Subnets** (public and private)
- 🚪 **Route tables** (traffic rules)
- 🔒 **Security** (firewalls at multiple levels)

Think of VPC as **building a gated community** inside AWS — you decide who gets in, who gets out, and who talks to whom.

---

## Why Do We Need VPC?

Without VPC, all your AWS resources would be on a **public, shared network**. That's like:

- 🏠 Leaving your front door open to strangers
- 📦 Sharing a warehouse with random people who can touch your stuff
- 🚗 Driving on a road where everyone has access to your car

VPC gives you **isolation, security, and control** over your cloud network.

---

## Real-World Analogy

Ravi, imagine a **gated community**:

| Community Feature | VPC Equivalent |
|-------------------|----------------|
| Community walls | VPC boundary |
| Streets inside | Subnets |
| Main gate (entrance/exit) | Internet Gateway |
| Security guard at the gate | Security Groups |
| Road rules (who can use which road) | Route Tables |
| Toll booth for outbound traffic | NAT Gateway |
| Houses | EC2 Instances, RDS, etc. |

---

## VPC Key Components

### 1. CIDR Block

When you create a VPC, you assign a **CIDR (Classless Inter-Domain Routing) block** — your IP address range.

```
VPC CIDR: 10.0.0.0/16
This gives you: 10.0.0.0 — 10.0.255.255
Total IPs: 65,536 addresses
```

### 2. Subnets

Subnets divide your VPC into **smaller networks**. Each subnet lives in **one specific AZ**.

| Subnet Type | Access | Use For |
|-------------|--------|---------|
| **Public Subnet** | Has route to Internet Gateway | Web servers, load balancers |
| **Private Subnet** | NO route to Internet Gateway | Databases, app servers, caches |

### 3. Internet Gateway (IGW)

An **Internet Gateway** is a VPC component that allows communication between your VPC and the internet.

- 📥 **Inbound**: Traffic from the internet can reach resources in public subnets
- 📤 **Outbound**: Resources in public subnets can reach the internet

### 4. NAT Gateway

A **NAT Gateway** allows instances in **private subnets** to access the internet for **outbound-only** traffic (e.g., downloading updates) while remaining hidden from inbound connections.

```
Private Subnet ──► NAT Gateway ──► Internet
(Outbound OK)                        (Can't initiate connections TO private subnet)
```

---

## Security Groups vs NACLs

Ravi, this is one of the **most important distinctions** in AWS networking!

| Feature | Security Groups | NACLs |
|---------|----------------|-------|
| Level | **Instance-level** | **Subnet-level** |
| Type | **Stateful** | **Stateless** |
| Rules | Allow rules ONLY | Allow AND Deny rules |
| Default | All traffic ALLOWED | All traffic DENIED then ALLOWED |
| Scope | Attached to an instance | Applied to an entire subnet |
| Evaluation | All rules evaluated | Rules evaluated in order |

### What "Stateful" vs "Stateless" Means

**Security Groups (Stateful):**
- If you allow INBOUND traffic on port 443, the OUTBOUND response is **automatically allowed**
- You only need to define rules in ONE direction

**NACLs (Stateless):**
- If you allow INBOUND traffic on port 443, you must ALSO explicitly allow OUTBOUND traffic on the ephemeral ports
- Rules must be defined in **BOTH directions**

---

## How Internet Access Works

```
┌──────────────── VPC (10.0.0.0/16) ────────────────┐
│                                                      │
│  ┌─── Public Subnet (10.0.1.0/24) ───┐             │
│  │  Web Server (10.0.1.10)            │             │
│  │  ┌──────────┐                      │             │
│  │  │ Security │                      ├────► IGW ──► Internet
│  │  │ Group    │                      │             │
│  │  └──────────┘                      │             │
│  └────────────────────────────────────┘             │
│                                                      │
│  ┌─── Private Subnet (10.0.2.0/24) ──┐             │
│  │  App Server (10.0.2.10)            │             │
│  │  ┌──────────┐                      │             │
│  │  │ Security │                      ├────► NAT GW ──► Internet
│  │  │ Group    │                      │   (outbound only)
│  │  └──────────┘                      │             │
│  │  RDS Database (10.0.2.50)          │             │
│  └────────────────────────────────────┘             │
└──────────────────────────────────────────────────────┘
```

---

## Architecture: VPC with Public & Private Subnets

This is the **most common production VPC design**:

| Layer | Subnet | Resources | Internet Access |
|-------|--------|-----------|-----------------|
| **Web Tier** | Public | ALB, Bastion Host | Via IGW (both directions) |
| **App Tier** | Private | EC2 App Servers | Via NAT (outbound only) |
| **Data Tier** | Private | RDS, ElastiCache | No internet access |

---

## Route Tables

Route tables control **where traffic goes**.

| Destination | Target | Purpose |
|-------------|--------|---------|
| `10.0.0.0/16` | Local | Traffic within VPC stays local |
| `0.0.0.0/0` | Internet Gateway | Public subnet → Internet |
| `0.0.0.0/0` | NAT Gateway | Private subnet → Internet (outbound) |

---

## Best Practices for VPC

- ✅ **Use private subnets for databases** and backend services
- ✅ **Use a NAT Gateway** for private subnet internet access
- ✅ **Plan CIDR ranges carefully** — overlapping ranges cause issues when peering VPCs
- ✅ **Use Security Groups** as your primary firewall (stateful is easier to manage)
- ✅ **Use NACLs** as a secondary defense for subnet-level blocking
- ✅ **Use a bastion host** (jump box) in a public subnet for SSH access to private instances
- ✅ **Enable VPC Flow Logs** for network monitoring and troubleshooting

---

## Common Mistakes

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Putting everything in public subnets | Exposes databases and app servers to the internet | Use private subnets for backend resources |
| Not planning CIDR ranges | Can't peer VPCs later if ranges overlap | Plan CIDR for all VPCs before creating |
| Confusing SG vs NACL | Security issues from wrong rule placement | Use SGs for instance control, NACLs for subnet-level |
| Using a single AZ subnet | No high availability | Deploy subnets across multiple AZs |
| Forgetting NAT costs | NAT Gateway charges per hour + data processed | Use VPC Endpoints for AWS service traffic |
| Not using VPC Endpoints | Traffic to AWS services goes through internet | Use Gateway/Interface endpoints to keep traffic private |

---

## VPC Endpoints

**VPC Endpoints** let you connect to AWS services **without going through the internet**.

| Endpoint Type | Used For | Example |
|--------------|----------|---------|
| **Gateway Endpoint** | S3, DynamoDB | Free to use! |
| **Interface Endpoint** | Most AWS services (via PrivateLink) | Small hourly cost |

---

## Interview Questions

### Q1: What is the difference between a public and private subnet?
**Answer:** A **public subnet** has a route to an Internet Gateway, allowing resources to be accessed from and access the internet. A **private subnet** has no route to an Internet Gateway — resources cannot be accessed from the internet directly. Private subnets use a NAT Gateway for outbound-only internet access.

### Q2: Explain Security Groups vs NACLs.
**Answer:** **Security Groups** are stateful, instance-level firewalls that only support ALLOW rules. They automatically allow return traffic. **NACLs** are stateless, subnet-level firewalls that support both ALLOW and DENY rules. They require explicit rules for both inbound and outbound traffic. Security Groups are the primary defense; NACLs are a secondary layer.

### Q3: Why should you use private subnets for databases?
**Answer:** Databases contain sensitive data. Placing them in private subnets means they have **no public IP address** and **no route to the internet**. They can only be accessed from within the VPC (e.g., by app servers), dramatically reducing the attack surface.

### Q4: How do you connect two VPCs together?
**Answer:** **VPC Peering** connects two VPCs via a direct network route. Traffic stays on the AWS backbone (no internet). Each VPC's CIDR ranges must **not overlap**. For connecting many VPCs, use **AWS Transit Gateway** instead.

### Q5: What are VPC Endpoints and why use them?
**Answer:** VPC Endpoints allow you to access AWS services (S3, DynamoDB, etc.) **privately** without routing traffic through the internet or a NAT Gateway. **Gateway Endpoints** (for S3 and DynamoDB) are free. **Interface Endpoints** use PrivateLink for other services.

---

## Summary

| Concept | Key Takeaway |
|---------|-------------|
| **VPC** | Your isolated private network in AWS |
| **Subnets** | Divide VPC into public/private zones |
| **Internet Gateway** | Connects public subnets to the internet |
| **NAT Gateway** | Gives private subnets outbound internet access |
| **Security Groups** | Stateful, instance-level, ALLOW only |
| **NACLs** | Stateless, subnet-level, ALLOW + DENY |
| **CIDR** | Plan your IP ranges carefully |
| **VPC Endpoints** | Access AWS services without internet |

---

> 📝 **Rithu says:** *"Ravi, VPC is the foundation of everything in AWS. Get networking right, and everything else becomes easier. Always use private subnets for anything sensitive!"*

---

**Next:** [12 - Route 53 (DNS)](../12%20-%20Route%2053%20-%20DNS/README.md) → Let's learn about DNS in AWS! 🌍
