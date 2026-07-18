# 07 - Auto Scaling Group (ASG)

![Phase-2 Compute](https://img.shields.io/badge/Phase-2%20Compute-purple)
![Difficulty](https://img.shields.io/badge/Difficulty-%E2%AD%90%E2%AD%90%E2%AD%90%20Advanced-red)
![Read Time](https://img.shields.io/badge/Read%20Time-~12%20min-blue)

---

## What is an Auto Scaling Group?

Hey Ravi! Imagine running a restaurant. On Monday, you have 2 customers and 10 chefs standing idle 💸. On Friday, you have 100 customers and your 1 chef is drowning 🍳. Wouldn't it be magical if chefs **automatically appeared** when it's busy and **went home** when it's quiet?

That's exactly what **Auto Scaling Group (ASG)** does for your EC2 instances! It automatically adjusts the number of instances based on demand — scaling out when traffic spikes and scaling in when it drops.

---

## Why Do We Need ASG?

Without ASG, you'd face these pain points:

| Problem | Without ASG | With ASG |
|---------|------------|----------|
| Traffic spike | Server crashes 😱 | More instances added automatically |
| Traffic drops | Idle servers wasting money 💸 | Instances removed to save cost |
| Server dies | Manual intervention needed | Auto-replaced in seconds |
| Deployment | Downtime during updates | Rolling/blue-green deploys |

> **Ravi, think of it this way:** Without ASG, you're babysitting servers 24/7. With ASG, AWS babysits them FOR you!

---

## Real-World Analogy

Ravi, here's how I explain ASG to my friends:

**ASG is like an auto-pilot pilot for your EC2 fleet** ✈️

- **Rush hour (high traffic):** The pilot adds more planes to carry passengers
- **Quiet hours (low traffic):** The pilot parks extra planes in the hangar
- **Plane breaks down (unhealthy instance):** The pilot immediately replaces it with a fresh one
- **You (the DevOps engineer):** You just set the rules — "I need 2-5 planes, keep CPU under 50%"

---

## Key Concepts

Before diving in, let's nail the terminology:

### 1. Launch Template
A **blueprint** for new instances. It defines:
- AMI ID (which OS image)
- Instance type (t3.micro, m5.large, etc.)
- Security groups
- Key pair
- User data script

> ⚠️ **Important:** Use **Launch Templates** (not Launch Configurations). Launch Configurations are legacy and deprecated!

### 2. Capacity Settings

| Setting | Description | Example |
|---------|-------------|---------|
| **Min** | Minimum instances always running | 2 |
| **Max** | Maximum instances allowed | 10 |
| **Desired** | Current target number | 4 |

The ASG always tries to keep the instance count at the **Desired** number. It will never go below **Min** or above **Max**.

### 3. Health Checks
ASG monitors instance health and automatically replaces unhealthy ones.

---

## Scaling Policies — The Brains of ASG

This is the core part, Ravi! Scaling policies tell ASG **when** to add or remove instances.

### Target Tracking Scaling (Most Common 🌟)

You set a target metric, and ASG automatically adjusts capacity to keep it there.

```
Keep average CPU utilization at 50%

Traffic ↑ → CPU hits 70% → ASG adds instances → CPU drops back to 50%
Traffic ↓ → CPU drops to 20% → ASG removes instances → CPU rises back to 50%
```

**Common targets:**
- CPU Utilization (most popular)
- Request count per target
- Average memory utilization

### Step Scaling

You define CloudWatch alarms and "steps" for how many instances to add:

```
If CPU > 70%  → Add 1 instance
If CPU > 85%  → Add 3 instances
If CPU > 95%  → Add 5 instances
```

### Scheduled Scaling

For **predictable traffic patterns** (e.g., daily peak at 9 AM):

```
Schedule: Scale to 10 instances every weekday at 8:00 AM
Schedule: Scale to 2 instances every weekday at 8:00 PM
```

### Comparison Table

| Policy | Best For | Complexity |
|--------|----------|------------|
| **Target Tracking** | Most use cases | ⭐ Easy |
| **Step Scaling** | Fine-grained control | ⭐⭐ Medium |
| **Scheduled Scaling** | Predictable patterns | ⭐ Easy |
| **Predictive Scaling** | ML-based forecasting | ⭐⭐ Medium |

---

## How ASG Handles Health Checks

When an instance fails a health check, here's what happens:

1. 🔍 ASG detects unhealthy instance (via ELB health check or EC2 status check)
2. ⏳ Waits for **health check grace period** (so the app has time to start)
3. 🔄 Launches a **new instance** from the launch template
4. 📋 Registers it with the load balancer
5. 🗑️ Terminates the unhealthy instance

> **Grace Period Tip:** Set this to at least 300 seconds for apps that take time to initialize. Too short = ASG kills instances before they're ready!

---

## Architecture Overview

Here's how ASG works in a production setup:

```
                    ┌─────────────────────┐
                    │     Users/Traffic    │
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │   Application Load   │
                    │     Balancer (ALB)   │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
     ┌────────▼──────┐ ┌──────▼────────┐ ┌─────▼───────┐
     │  EC2 Instance │ │ EC2 Instance  │ │ EC2 Instance│
     │   (AZ-1a)     │ │  (AZ-1b)      │ │  (AZ-1c)    │
     └───────────────┘ └───────────────┘ └─────────────┘
              │                │                │
              └────────────────┼────────────────┘
                               │
                    ┌──────────▼──────────┐
                    │   Auto Scaling      │
                    │   Group (ASG)       │
                    │                     │
                    │  Min: 2  Max: 10    │
                    │  Desired: 3         │
                    │  Scaling: Target 50%│
                    └─────────────────────┘
```

Key architecture points:
- ASG spans **multiple AZs** for high availability
- ALB distributes traffic across instances
- ASG replaces unhealthy instances automatically
- All instances use the same **Launch Template**

---

## Common Use Cases

| Use Case | Why ASG? |
|----------|---------|
| **Web Applications** | Handle traffic spikes, maintain HA |
| **API Servers** | Scale based on request count |
| **Background Workers** | Scale based on queue depth |
| **Microservices** | Independent scaling per service |
| **Batch Processing** | Scheduled scaling for known workloads |
| **CI/CD Build Agents** | Scale up during builds, down after |

---

## Best Practices

1. 🌍 **Always use 2+ AZs** — One AZ can fail; spread instances for HA
2. ⏱️ **Set proper health check grace period** — Give apps time to initialize (300s minimum)
3. 📝 **Use Launch Templates** — Not Launch Configurations (they're deprecated)
4. 🎯 **Start with Target Tracking** — Easiest to set up and works for most cases
5. 🏷️ **Tag your ASGs** — Makes cost tracking and management easier
6. 💰 **Set sensible Max** — Prevents cost explosion during runaway scaling
7. 📊 **Use Lifecycle Hooks** — For graceful instance draining during scale-in
8. 🔐 **Use instance profiles** — Never hardcode credentials in launch templates

---

## Common Mistakes

| Mistake | Impact | Fix |
|---------|--------|-----|
| Setting Max too high | Cost explosion 💸 | Set reasonable Max based on budget |
| Single AZ deployment | Single point of failure 💥 | Always use 2+ AZs |
| No health check grace period | Instances killed before ready 🚫 | Set 300s+ grace period |
| Using Launch Configurations | Deprecated, missing features | Switch to Launch Templates |
| Ignoring scale-in protection | Removing critical instances 🚨 | Use instance protection for essential instances |
| No warm-up strategy | Cold instances hurt performance | Use lifecycle hooks + user data for warm-up |

---

## Interview Questions

### Q1: What are the three key parameters of an Auto Scaling Group?
**Answer:** **Min** (minimum instances — never go below this), **Desired** (target count — ASG tries to maintain this), and **Max** (maximum instances — never exceed this). ASG adjusts the desired capacity based on scaling policies, but always respects min and max bounds.

### Q2: What's the difference between Target Tracking and Step Scaling?
**Answer:** **Target Tracking** is like a thermostat — you set a target (e.g., "keep CPU at 50%") and ASG automatically adjusts. **Step Scaling** gives you more control — you define specific thresholds and how many instances to add at each step (e.g., "at 70% add 1, at 85% add 3"). Target Tracking is simpler and preferred for most use cases.

### Q3: Why should you use Launch Templates instead of Launch Configurations?
**Answer:** Launch Configurations are **deprecated** and lack modern features. Launch Templates support **versioning**, **mixed instance types**, **T2/T3 unlimited mode**, and better integration with newer AWS services. Always use Launch Templates for new deployments.

### Q4: What is a Health Check Grace Period and why does it matter?
**Answer:** It's the time ASG waits after launching a new instance before it starts checking its health. If set too short, ASG might terminate healthy instances that are still starting up (e.g., Java apps that take 5 minutes to warm up). A typical value is 300 seconds.

### Q5: How does ASG work with an Application Load Balancer?
**Answer:** ASG registers new instances with the ALB's target group automatically. When scaling out, new instances are added to the target group. When scaling in, instances are drained (connections moved) and removed. ALB health checks feed into ASG — if ALB marks an instance unhealthy, ASG replaces it.

---

## Summary

| Concept | Key Takeaway |
|---------|-------------|
| **ASG** | Automatically manages EC2 instance count |
| **Launch Template** | Blueprint for new instances |
| **Target Tracking** | "Keep metric at X%" — easiest policy |
| **Health Checks** | Auto-replace unhealthy instances |
| **Multi-AZ** | Always spread across 2+ AZs |
| **Max Cap** | Prevents runaway costs |

---

**Next:** [08 - Elastic Load Balancer](../08%20-%20Elastic%20Load%20Balancer/README.md) → Learn how to distribute traffic across your scaled instances! 🌐
