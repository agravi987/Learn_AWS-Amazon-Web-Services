# 12 - Route 53 (DNS)

![Phase-4-Networking](https://img.shields.io/badge/Phase-4-Networking-orange) ![Difficulty-Medium](https://img.shields.io/badge/Difficulty-⭐⭐-Medium-yellow) ![ReadTime](https://img.shields.io/badge/Read%20Time-~10%20min-blue)

> *"Ravi, when you type `example.com` in your browser, how does your computer find the right server?"*
> *"Umm... it just works?"*
> *"Magic的背后, Ravi — there's a phone book called DNS. And AWS has its own version: Route 53!"* 📞

---

## What is Route 53?

**Amazon Route 53** is AWS's **Domain Name System (DNS)** web service. The name comes from the DNS standard port **53**.

Route 53 does three main things:
1. 🌍 **Domain Registration** — Buy and manage domain names
2. 🔄 **DNS Routing** — Translate domain names to IP addresses
3. ❤️ **Health Checking** — Monitor the health of resources

---

## Why Do We Need Route 53?

Computers talk using **IP addresses** (like `54.239.28.85`), but humans prefer **domain names** (like `www.amazon.com`).

DNS is the **phone book of the internet** — it translates human-readable names into machine-readable addresses.

Without DNS, you'd have to memorize IP addresses for every website. Imagine telling your friend: *"Visit 54.239.28.85"* instead of *"Visit amazon.com"* 😵

---

## Real-World Analogy

Ravi, think of Route 53 like a **contact list on your phone**:

- 📱 You type **"Mom"** (domain name)
- 📞 Your phone looks up the number (**IP address**) and dials
- 🔍 DNS does exactly this — looks up the name, finds the number, connects you

Route 53 is AWS's **giant contact list** that the whole internet can use!

---

## How DNS Works (Simplified)

```
User types www.example.com
        │
        ▼
┌─── DNS Resolver (ISP) ───┐
│  "Do I know this?"        │
│  If not, ask root server  │
└───────────┬───────────────┘
            │
            ▼
┌─── Route 53 (Authoritative) ──┐
│  "www.example.com = 54.239..." │
└───────────┬────────────────────┘
            │
            ▼
    Browser connects to
    54.239.28.85
```

---

## DNS Record Types

| Record Type | Maps To | Example | Use Case |
|-------------|---------|---------|----------|
| **A** | Name → IPv4 | `example.com → 54.239.28.85` | Most common — maps domain to server IP |
| **AAAA** | Name → IPv6 | `example.com → 2600:...` | IPv6 address mapping |
| **CNAME** | Name → Another Name | `www.example.com → example.com` | Alias one domain to another |
| **MX** | Name → Mail Server | `example.com → mail.example.com` | Email routing |
| **TXT** | Name → Text | SPF, DKIM records | Email verification, domain validation |
| **NS** | Name → Name Servers | `example.com → ns1.route53.amazonaws.com` | Delegates DNS to specific servers |
| **SOA** | Start of Authority | Zone metadata | Admin info about the DNS zone |

---

## Alias Records (AWS Special Feature)

**Alias records** are a Route 53 special feature that maps a domain name to an **AWS resource** directly.

| Feature | Standard DNS (CNAME) | Route 53 Alias |
|---------|---------------------|----------------|
| Maps to | Any hostname | AWS resources (ALB, CloudFront, S3, etc.) |
| Works at zone apex? | ❌ No (`example.com` can't be CNAME) | ✅ Yes! |
| Cost | Standard DNS query charges | **Free** for AWS resources |
| Health checked? | Manual setup | Automatic with health checks |

### Example
```
example.com  ──Alias──►  my-alb-123456.us-east-1.elb.amazonaws.com
                         (Automatically resolves to current IP)
```

---

## Routing Policies

Route 53 doesn't just resolve DNS — it lets you **control HOW traffic is routed**!

### 1. Simple Routing
- One record, one answer
- Default behavior — just resolve to one resource

### 2. Weighted Routing
- Split traffic by **percentage**
- Example: 80% to v1, 20% to v2 (canary deployments)

```
         ┌──► Server v1 (80%) ──┐
Traffic ─┤                       ├──► Same App
         └──► Server v2 (20%) ──┘
```

### 3. Latency-Based Routing
- Route users to the **server with the lowest latency**
- Great for global applications

### 4. Failover Routing
- Primary + Secondary (backup) resources
- If primary fails health check → traffic goes to secondary

```
Traffic ──► Primary (healthy) ✅
              │
              ▼ (fails health check)
            Secondary (backup) 🔄
```

### 5. Geolocation Routing
- Route based on **user's geographic location**
- Example: US users → US servers, EU users → EU servers

### 6. Multivalue Answer
- Return **multiple healthy IP addresses**
- Client picks one at random
- Simple load balancing via DNS

---

## Health Checks

Route 53 can **monitor your endpoints** and automatically remove unhealthy ones from DNS responses.

| Check Type | What It Monitors |
|-----------|-----------------|
| **HTTP/HTTPS** | Status code, response time |
| **TCP** | Port connectivity |
| **Calculated** | Combine multiple health checks |

**Why it matters:** Without health checks, DNS might keep sending users to a dead server.

---

## Domain Registration

Route 53 can also **register new domains** directly:

- `example.com` → Register for ~$12/year
- Route 53 handles renewal, transfers, and DNS configuration
- You can also use Route 53 as DNS for domains registered elsewhere

---

## Best Practices for Route 53

- ✅ **Use Alias records** for AWS resources (free health checks + works at zone apex)
- ✅ **Set up health checks** for failover and weighted routing
- ✅ **Use TTL wisely** — lower TTL = faster updates, but more DNS queries (higher cost)
- ✅ **Use geolocation routing** for global apps to serve users from the nearest region
- ✅ **Enable DNSSEC** for security against DNS spoofing
- ✅ **Use weighted routing** for gradual deployments and A/B testing
- ✅ **Monitor with CloudWatch** — Route 53 metrics are available in CloudWatch

---

## Common Mistakes

| Mistake | Why It's Bad | Fix |
|---------|-------------|-----|
| Not using Alias for AWS resources | Paying for CNAME queries + can't use at zone apex | Always use Alias for ALB, CloudFront, S3 |
| Setting TTL too high | Changes take too long to propagate | Use 60-300s TTL for dynamic apps |
| Setting TTL too low | Higher costs, slower initial lookups | Use 3600s+ for stable, rarely-changing records |
| Ignoring health checks | Dead endpoints keep getting traffic | Always set up health checks for critical resources |
| Not planning DNS migration | Complex migration when switching providers | Plan DNS strategy early |

---

## Interview Questions

### Q1: What is the difference between a CNAME and an Alias record?
**Answer:** A **CNAME** maps one hostname to another hostname and **cannot be used at the zone apex** (root domain). An **Alias** record maps a domain to an **AWS resource** (ALB, S3, CloudFront) and **works at the zone apex**. Alias records for AWS resources are **free**.

### Q2: When would you use Weighted Routing?
**Answer:** Weighted routing is perfect for **canary deployments** and **A/B testing**. You can gradually shift traffic from one version to another — e.g., send 10% of traffic to the new version first, then increase gradually.

### Q3: What happens when a Route 53 health check fails?
**Answer:** Route 53 **removes the unhealthy resource from DNS responses**. In failover routing, traffic automatically routes to the secondary resource. In weighted routing, traffic redistributes among healthy endpoints.

### Q4: What is a TTL in DNS and why does it matter?
**Answer:** **TTL (Time To Live)** tells DNS resolvers how long (in seconds) to cache a record before asking Route 53 again. **Low TTL** (e.g., 60s) = faster propagation of changes but more queries (higher cost). **High TTL** (e.g., 3600s) = fewer queries but slower change propagation.

### Q5: How does Route 53 achieve high availability?
**Answer:** Route 53 is built on **100% availability SLA**. It uses a **global network of DNS servers** with **anycast routing**, meaning queries are automatically routed to the nearest healthy server. It's one of the most reliable AWS services.

---

## Summary

| Concept | Key Takeaway |
|---------|-------------|
| **Route 53** | AWS's DNS service (named after port 53) |
| **A Record** | Maps domain name to IPv4 address |
| **AAAA Record** | Maps domain name to IPv6 address |
| **CNAME** | Maps hostname to hostname (not at zone apex) |
| **Alias** | Maps domain to AWS resource (free, works at apex) |
| **Weighted** | Split traffic by percentage |
| **Latency** | Route to lowest-latency server |
| **Failover** | Primary → backup automatic switching |
| **Health Checks** | Monitor endpoints, auto-remove unhealthy ones |
| **TTL** | Cache duration — balance cost vs propagation speed |

---

> 📝 **Rithu says:** *"Ravi, Route 53 is like the phone book of the cloud. Master DNS, and you'll understand how the internet actually connects users to servers. Always use Alias records for AWS resources!"*

---

**Next:** [13 - RDS (Relational Database Service)](../13%20-%20RDS%20-%20Relational%20Database%20Service/README.md) → Let's learn about managed databases! 🗄️
