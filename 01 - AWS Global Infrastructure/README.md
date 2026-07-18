# 01 — AWS Global Infrastructure

![Phase](https://img.shields.io/badge/Phase-1%20Foundations-blue?style=flat-square)
![Difficulty](https://img.shields.io/badge/Difficulty-⭐%20Beginner-brightgreen?style=flat-square)
![Read%20Time](https://img.shields.io/badge/Read%20Time-~12%20min-yellow?style=flat-square)

> *"Ravi, AWS has data centers ALL over the world. Before you deploy anything, you need to understand WHERE it lives and WHY that matters."* — **Rithu** 🌍

---

## What is AWS Global Infrastructure?

AWS operates a **global network of data centers** organized into Regions, Availability Zones, and Edge Locations. This is the physical backbone that makes everything else possible.

---

## Why Do We Need It?

- **Latency:** Users in India shouldn't connect to a server in the USA
- **Compliance:** Some data must stay in a specific country (e.g., Indian banking data must stay in India)
- **Disaster Recovery:** If one data center burns down, your app survives in another
- **High Availability:** Spread resources across multiple locations

---

## Real-World Analogy

> Think of AWS like **Amazon's delivery network:**
>
> - **Region** = A city with a warehouse (Mumbai, Singapore, etc.)
> - **Availability Zone (AZ)** = Individual warehouses within that city (3-6 per city, connected by roads)
> - **Edge Location** = Delivery lockers in neighborhoods (for fast last-mile delivery)
> - **Local Zone** = Pop-up stores closer to specific customers

---

## How It Works

### Regions

A **Region** is a geographic area with **multiple isolated data centers**.

```
  🌍 AWS Regions (33+ worldwide)
  ┌─────────────────────────────────────────────────┐
  │                                                 │
  │  🇮🇳 ap-south-1      (Mumbai)                    │
  │  🇸🇬 ap-southeast-1   (Singapore)                │
  │  🇺🇸 us-east-1        (N. Virginia)              │
  │  🇺🇸 us-west-2        (Oregon)                   │
  │  🇩🇪 eu-central-1     (Frankfurt)                │
  │  🇯🇵 ap-northeast-1   (Tokyo)                    │
  │  ... 33+ more                                   │
  └─────────────────────────────────────────────────┘
```

**How to choose a Region:**

| Factor | Consideration |
|:-------|:-------------|
| **Latency** | Pick the region closest to your users |
| **Compliance** | Some laws require data to stay in a specific country |
| **Services** | Not all services are available in all regions |
| **Cost** | Pricing varies by region (us-east-1 is usually cheapest) |

---

### Availability Zones (AZs)

Each Region has **3-6 AZs**. An AZ is one or more **physically separate data centers** with independent power, networking, and cooling.

```
  Region: ap-south-1 (Mumbai)
  ┌──────────────────────────────────────────┐
  │                                          │
  │  ┌──────────┐  ┌──────────┐  ┌──────────┐│
  │  │ AZ-1a    │  │ AZ-1b    │  │ AZ-1c    ││
  │  │DC1 DC2 DC3│  │DC4 DC5 DC6│  │DC7 DC8 DC9││
  │  └────┬─────┘  └────┬─────┘  └────┬─────┘│
  │       │              │              │      │
  │       └────── High-Speed Fiber ─────┘      │
  │              (Low-latency network)          │
  └──────────────────────────────────────────┘
```

**Why AZs matter:**

- If AZ-1a loses power → AZ-1b and AZ-1c are fine
- Deploy across **multiple AZs** = high availability
- AWS recommends **at least 2 AZs** for production

---

### Edge Locations

**Edge Locations** are caching points closer to end users (200+ worldwide).

```
  User in Delhi requests your website
        │
        ▼
  ┌──────────────┐     ┌──────────────────┐
  │ Edge Location │────▶│ Your Server      │
  │ (Delhi)       │     │ (Mumbai Region)  │
  └──────────────┘     └──────────────────┘
        │                      │
        ▼                      │
  First request: ──────────────┘
  "Get the data from Mumbai"

  Second request: ◀── Already cached here!
  "Serve from Delhi Edge Location"
  Super fast! ⚡
```

Used by **CloudFront** (CDN) and **Route 53** (DNS).

---

## Key Features

| Feature | What It Means |
|:--------|:-------------|
| **Regions** | 33+ geographic areas |
| **Availability Zones** | 3-6 isolated data centers per Region |
| **Edge Locations** | 200+ caching points globally |
| **Local Zones** | Extensions of Regions closer to users |
| **Wavelength Zones** | For ultra-low latency (5G networks) |

---

## Architecture Overview

```
  ┌─────────────────────────────────────────────────────────┐
  │                    AWS Global                            │
  │                                                         │
  │   ┌──────────────────────────────────────────────────┐  │
  │   │              Region: Mumbai                       │  │
  │   │  ┌─────────┐  ┌─────────┐  ┌─────────┐          │  │
  │   │  │  AZ-1a  │  │  AZ-1b  │  │  AZ-1c  │          │  │
  │   │  │ EC2     │  │ EC2     │  │ EC2     │          │  │
  │   │  │ RDS     │  │ RDS     │  │         │          │  │
  │   │  └─────────┘  └─────────┘  └─────────┘          │  │
  │   └──────────────────────────────────────────────────┘  │
  │                                                         │
  │   ┌──────────────────────────────────────────────────┐  │
  │   │              Region: Singapore                    │  │
  │   │  ┌─────────┐  ┌─────────┐                        │  │
  │   │  │  AZ-1a  │  │  AZ-1b  │                        │  │
  │   │  │ EC2     │  │ EC2     │                        │  │
  │   │  └─────────┘  └─────────┘                        │  │
  │   └──────────────────────────────────────────────────┘  │
  │                                                         │
  │   Edge Locations: 📍 200+ cities worldwide              │
  └─────────────────────────────────────────────────────────┘
```

---

## Common Use Cases

- **Multi-Region Deployment:** Deploy in Mumbai + Singapore for global users
- **Disaster Recovery:** Primary in Mumbai, backup in Singapore
- **Low Latency:** Use Edge Locations with CloudFront to serve content fast
- **Compliance:** Keep Indian user data in ap-south-1 (Mumbai)

---

## Best Practices

1. **Choose Region based on:** Latency → Compliance → Cost (in that order)
2. **Deploy across 2+ AZs** for production workloads
3. **Use CloudFront** for global content delivery
4. **Replicate data** across regions for disaster recovery
5. **Check service availability** — not every service exists in every region

---

## Common Mistakes

| Mistake | Why It's Bad | Fix |
|:--------|:------------|:----|
| Deploying in us-east-1 by default | Might be far from your users | Choose region based on user location |
| Single AZ deployment | One data center down = app down | Always use 2+ AZs |
| Ignoring compliance | Legal penalties for data sovereignty | Check data residency laws |
| Not considering cost | Prices vary 20-30% between regions | Compare pricing before choosing |

---

## Interview Questions

1. **What is an AWS Region?**
   > A geographic area with multiple isolated data centers (AZs) designed for fault tolerance and stability.

2. **What is an Availability Zone?**
   > One or more physically separate data centers within a Region, with independent power and networking, connected by low-latency fiber.

3. **How many AZs should you use for production?**
   > At least 2 AZs for high availability. AWS recommends spreading load across multiple AZs.

4. **What is an Edge Location?**
   > A caching endpoint closer to end users, used by CloudFront (CDN) and Route 53 (DNS) for faster content delivery.

5. **How do you choose a Region?**
   > Based on: (1) Latency to users, (2) Compliance requirements, (3) Service availability, (4) Cost.

---

## Summary

| Concept | Key Point |
|:--------|:----------|
| **Region** | Geographic area with multiple data centers |
| **AZ** | Isolated data center(s) within a Region (3-6 per Region) |
| **Edge Location** | Caching points for faster content delivery (200+) |
| **Choose Region by** | Latency → Compliance → Cost |
| **Production rule** | Always use 2+ AZs |

---

**Next:** [02 — AWS Well-Architected Framework](../02%20-%20AWS%20Well-Architected%20Framework/) → *The 6 rules every cloud architect must know*

---

> *"Ravi, knowing where your stuff lives is like knowing where the exit is in a movie theater. You don't think about it until there's a fire. Then you're VERY glad you knew."* — **Rithu** 🔥🏃
