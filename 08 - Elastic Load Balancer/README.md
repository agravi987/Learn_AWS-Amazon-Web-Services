# 08 - Elastic Load Balancer (ELB)

![Phase-2 Compute](https://img.shields.io/badge/Phase-2%20Compute-purple)
![Difficulty](https://img.shields.io/badge/Difficulty-%E2%AD%90%E2%AD%90%20Medium-yellow)
![Read Time](https://img.shields.io/badge/Read%20Time-~12%20min-blue)

---

## ALB Architecture Overview

![Application Load Balancer Architecture](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/images/component_architecture.png)

> *The components of a basic Application Load Balancer.*

---

## What is an Elastic Load Balancer?

Hey Ravi! You've got your EC2 instances running, maybe even an Auto Scaling Group. But here's the thing — how do users actually reach those instances? You can't just hand out individual IP addresses to users!

That's where **Elastic Load Balancer (ELB)** comes in. It automatically distributes incoming traffic across multiple targets (EC2 instances, containers, IPs) to ensure no single server gets overwhelmed.

> **One DNS name → ELB → Many backend servers.** Users only need to know one address!

---

## Why Do We Need ELB?

| Problem | Without ELB | With ELB |
|---------|------------|----------|
| Single server | One point of failure 💥 | Traffic distributed across multiple |
| Uneven load | One server handles everything 😰 | Balanced across all servers |
| SSL management | Manual cert install on each server | One place to manage SSL ✅ |
| Health monitoring | Manual checks | Auto-routes away from unhealthy servers |
| Scaling | Need to update DNS manually | Works seamlessly with ASG |

> **Ravi's Rule:** If you have more than one server, you NEED a load balancer!

---

## Real-World Analogy

Ravi, think of a restaurant hostess 🍽️:

- **You (the user)** walk in and say "Table for 2, please"
- **The hostess (Load Balancer)** checks which tables are free
- **She seats you** at an available table (EC2 instance)
- **If a table is dirty** (unhealthy instance), she skips it
- **During rush hour**, she might call for more staff (works with ASG)

You never choose your own table — the hostess makes sure you get the best available one!

---

## Types of Load Balancers

AWS offers four types of load balancers. Let's break them down:

### 1. ALB (Application Load Balancer) — Layer 7 🌟

**The one you'll use most often!**

- Operates at **Layer 7 (HTTP/HTTPS)**
- Understands URLs, headers, cookies
- Perfect for **web applications, APIs, microservices**
- Supports **path-based** and **host-based** routing

| Feature | Details |
|---------|---------|
| Protocol | HTTP, HTTPS, WebSocket |
| Routing | Path, Host, HTTP Header, Query string |
| Use Case | Web apps, REST APIs, microservices |
| SSL Termination | ✅ Yes (at the load balancer) |

**Path-Based Routing Example:**
```
myapp.com/api/*    → Target Group A (API servers)
myapp.com/web/*    → Target Group B (Web servers)
myapp.com/images/* → Target Group C (Image servers)
```

### 2. NLB (Network Load Balancer) — Layer 4

- Operates at **Layer 4 (TCP/UDP/TLS)**
- Ultra-low latency (microseconds!)
- Handles **millions of requests per second**
- Preserves source IP address

| Feature | Details |
|---------|---------|
| Protocol | TCP, UDP, TLS |
| Latency | Microseconds |
| Use Case | Gaming, IoT, extreme performance, financial apps |
| Static IP | ✅ Yes (one per AZ) |

### 3. GLB (Gateway Load Balancer) — Layer 3

- Operates at **Layer 3 (Network/IP)**
- For deploying **third-party virtual appliances** (firewalls, IDS/IPS)
- Transparent — doesn't modify packets
- Used in specialized networking scenarios

### 4. CLB (Classic Load Balancer) — Legacy 🚫

- Old-generation load balancer
- **Don't use for new projects** — use ALB or NLB instead
- Still exists for legacy applications

### Quick Comparison

| Feature | ALB | NLB | GLB |
|---------|-----|-----|-----|
| **Layer** | 7 (HTTP) | 4 (TCP) | 3 (IP) |
| **Best For** | Web apps | Ultra-low latency | Virtual appliances |
| **SSL Termination** | ✅ | ✅ | ❌ |
| **WebSockets** | ✅ | ✅ | ❌ |
| **Static IP** | ❌ | ✅ | ❌ |
| **Cost** | 💰 | 💰💰 | 💰💰 |

> **DevOps Interview Tip, Ravi:** Most interviewers focus on **ALB vs NLB**. Remember — ALB for HTTP/web, NLB for TCP/performance!

---

## Key Components of ALB

Understanding these three components is essential:

### 1. Listener
A **listener** checks for connection requests using the protocol and port you configure.

```
Port 80  → HTTP Listener  → forwards to Target Group A
Port 443 → HTTPS Listener → forwards to Target Group B
```

### 2. Target Group
A **target group** routes requests to registered targets (EC2 instances, IPs, Lambda functions).

| Target Type | Use Case |
|------------|----------|
| **Instance** | EC2 instances (most common) |
| **IP** | On-premises servers, containers |
| **Lambda** | Serverless functions |

### 3. Health Checks
ALB periodically sends requests to targets to verify they're healthy.

```
Health Check Path: /health
Interval: 30 seconds
Healthy Threshold: 2 (2 consecutive successes = healthy)
Unhealthy Threshold: 3 (3 consecutive failures = unhealthy)
Timeout: 5 seconds
```

> **Ravi's Tip:** Always configure a meaningful health check endpoint (like `/health` or `/status`) in your application, not just `/` which might return 200 but the app could be broken!

---

## Architecture Overview

Here's how a typical ALB setup looks:

```
                    ┌─────────────────────────┐
                    │      Users (Internet)    │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │     Application Load     │
                    │     Balancer (ALB)       │
                    │                          │
                    │  Listener:443 (HTTPS)    │
                    │  Listener:80  (HTTP→443) │
                    └────────────┬────────────┘
                                 │
                    ┌────────────▼────────────┐
                    │     Target Group         │
                    │  Protocol: HTTP          │
                    │  Port: 8080              │
                    │  Health Check: /health   │
                    └────────────┬────────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
     ┌────────▼───────┐ ┌───────▼────────┐ ┌──────▼──────┐
     │  EC2 Instance  │ │  EC2 Instance  │ │ EC2 Instance│
     │   (AZ-1a)      │ │   (AZ-1b)      │ │  (AZ-1c)    │
     │  Port: 8080    │ │  Port: 8080    │ │  Port: 8080 │
     └────────────────┘ └────────────────┘ └─────────────┘
```

---

## ALB Features in Depth

### SSL/TLS Termination
- Terminate SSL **at the ALB** (decrypt traffic once)
- Backend servers receive plain HTTP (less CPU load on servers)
- Use **AWS Certificate Manager (ACM)** for free SSL certificates!

### Sticky Sessions (Session Affinity)
- A user's session stays on the same target
- Useful for apps that store session data locally
- **Warning:** Reduces load distribution effectiveness

### Cross-Zone Load Balancing
- Distributes traffic evenly across ALL targets in ALL AZs
- Without it, each AZ gets equal traffic (could be uneven if targets differ)

### Access Logs
- ALB can log all requests to S3
- Great for debugging, analytics, and security audits

---

## Common Use Cases

| Use Case | Load Balancer | Why |
|----------|--------------|-----|
| **Web Application** | ALB | HTTP routing, SSL, path-based |
| **Microservices API** | ALB | Route by path/host to different services |
| **Gaming Server** | NLB | Ultra-low latency, TCP/UDP |
| **IoT Data Ingestion** | NLB | Millions of connections |
| **Firewall Appliance** | GLB | Transparent packet inspection |
| **Legacy App** | CLB | Don't — migrate to ALB! |

---

## Best Practices

1. 🌍 **Use ALB for web apps** — It's the Swiss Army knife of load balancers
2. 💊 **Configure health checks properly** — Use a dedicated `/health` endpoint
3. 🔒 **Always use HTTPS** — Terminate SSL at ALB, redirect HTTP to HTTPS
4. 🛡️ **Use Security Groups** — ALB security group allows inbound 80/443; EC2 security group only allows traffic FROM ALB
5. 🌐 **Deploy across 2+ AZs** — For high availability
6. 📋 **Use access logs** — Send ALB logs to S3 for analysis
7. ⏱️ **Set appropriate timeouts** — Don't use default timeouts for long-running requests
8. 🏷️ **Tag ALBs** — For cost tracking and management

---

## Common Mistakes

| Mistake | Impact | Fix |
|---------|--------|-----|
| No health checks configured | Traffic sent to dead instances 💀 | Always configure health checks |
| Single AZ deployment | AZ failure = complete outage 💥 | Use 2+ AZs minimum |
| No HTTPS/SSL | Data transmitted in plaintext 😱 | Use ACM + ALB SSL termination |
| EC2 allows all inbound | Security risk 🔓 | EC2 SG should only allow ALB traffic |
| Using CLB for new projects | Missing modern features 🚫 | Use ALB or NLB instead |
| Health check path returns 200 always | ALB can't detect real issues 😅 | Use meaningful health check endpoints |

---

## Interview Questions

### Q1: What is the difference between ALB and NLB?
**Answer:** **ALB** operates at Layer 7 (HTTP/HTTPS) and supports path-based routing, host-based routing, and WebSocket. **NLB** operates at Layer 4 (TCP/UDP/TLS), offers ultra-low latency (microseconds), and supports static IP addresses. Use ALB for web apps and NLB for high-performance/low-latency scenarios like gaming or IoT.

### Q2: How does SSL/TLS termination work with ALB?
**Answer:** The ALB decrypts incoming HTTPS traffic using SSL certificates (from ACM). The decrypted traffic is then forwarded to backend targets as plain HTTP. This reduces CPU load on backend servers since they don't need to handle encryption/decryption. You still get end-to-end security if you re-encrypt between ALB and targets.

### Q3: What is a Target Group and how does it relate to a Listener?
**Answer:** A Target Group is a collection of targets (EC2, IPs, Lambda) that a Listener forwards traffic to. A Listener defines the protocol/port to listen on and rules for which Target Group to route to. One Listener can route to multiple Target Groups based on path, host, or header rules.

### Q4: How does ALB integrate with Auto Scaling Groups?
**Answer:** ALB and ASG work together seamlessly. When ASG launches new instances, it automatically registers them with the ALB's target group. Health checks from ALB feed into ASG — if ALB marks an instance unhealthy, ASG terminates it and launches a replacement. This creates a self-healing architecture.

### Q5: What is cross-zone load balancing and when should you use it?
**Answer:** Cross-zone load balancing distributes traffic evenly across all registered targets in **all Availability Zones**. Without it, traffic is distributed equally per AZ (regardless of how many targets are in each). Enable it when your AZs have unequal numbers of instances to prevent uneven load distribution.

---

## Summary

| Concept | Key Takeaway |
|---------|-------------|
| **ELB** | Distributes traffic across multiple targets |
| **ALB** | Layer 7 — for HTTP/web apps (most common) |
| **NLB** | Layer 4 — for TCP/ultra-low latency |
| **Target Group** | Collection of targets routed to by listeners |
| **Health Checks** | Auto-route away from unhealthy servers |
| **SSL Termination** | Decrypt at ALB, save backend CPU |

---

**Next:** [09 - S3 - Simple Storage Service](../09%20-%20S3%20-%20Simple%20Storage%20Service/README.md) → Enter the world of unlimited object storage! 🪣
