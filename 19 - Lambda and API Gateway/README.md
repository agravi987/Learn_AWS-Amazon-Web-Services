# AWS Lambda & API Gateway - Serverless Superpowers

![Phase-8%20Serverless-violet)](https://img.shields.io/badge/Phase--8%20Serverless-violet)
![Difficulty-⭐⭐%20Medium-yellow)](https://img.shields.io/badge/Difficulty-⭐⭐%20Medium-yellow)
![Read%20Time-~12%20min-blue)](https://img.shields.io/badge/Read%20Time-~12%20min-blue)

> "Why manage servers when AWS can do it for you?" - Every serverless developer

---

## What is Lambda?

Hey Ravi! Imagine you could run code **without ever provisioning or managing servers**. No patching, no scaling, no 3 AM wake-up calls because a server crashed. That's **AWS Lambda**!

Lambda is a **serverless, event-driven compute service**. You upload your code, and Lambda runs it **only when triggered**, scaling automatically. You pay **only for the compute time** you use. No code running = no cost!

---

## Feynman Test: Explain It Like You're 12

**Lambda is like a food delivery app.**

You don't own a restaurant (server). You just place an order (event). A chef (Lambda) somewhere cooks it (runs your code) and it arrives (response). You pay per meal (per request).

- The chef doesn't stand around idle waiting for orders. They **only start cooking when an order comes in**. That's Lambda!
- You don't pay for the chef's rent, electricity, or Netflix while they wait. You pay **only when food is being made**.
- If 100 orders come in at once, the delivery app doesn't say "sorry, one chef." It finds 100 chefs. Lambda does the same — **automatic scaling**.
- The chef has a 15-minute timer. If the dish isn't done by then, they stop. That's Lambda's **15-minute max timeout**.

> If you can explain this analogy to a friend, you understand Lambda.

---

## 80/20 Rule: Lambda

**80% of your Lambda knowledge comes from 20% of the concepts.** Focus here:

| Priority | Concept | Why It Matters |
|----------|---------|----------------|
| 1 | **Event-driven model** | Core mental model for serverless |
| 2 | **Cold starts** | #1 Lambda interview question |
| 3 | **API Gateway integration** | How Lambda serves real APIs |
| 4 | **Automatic scaling** | The "magic" of serverless |
| 5 | **15-min timeout + memory** | Hard limits you must know |

> Master these 5 concepts and you can handle 80% of Lambda interview questions confidently.

---

## Real-World Analogy: Food Delivery Chef

Ravi, think of it like this:

| Food Delivery | Lambda + API Gateway |
|--------------|---------------------|
| Delivery App | API Gateway (takes your order) |
| Chef | Lambda (cooks only when ordered) |
| Food Order | Event/Request (triggers the code) |
| Your House | Client (makes the request) |
| Kitchen | AWS Cloud (runs your code) |
| Pay per meal | Pay per request |

The chef doesn't stand around idle waiting for orders. They **only start cooking when an order comes in**. That's Lambda! You don't pay for idle time.

---

## Memory Tricks: Lambda

These are your **cheat codes** for remembering Lambda concepts:

### Lambda = "Less Drama"
**Lambda = Lame-bda? No! It's "less drama"** — no servers to manage. No patching, no scaling, no overnight on-call. Less drama, more shipping.

### Cold Start = Uber That's Far Away
**Cold Start = your Uber taking 5 min because it's far away.** The driver has to start the car, navigate to you, and drive over. That's the initialization overhead.
**Warm Start = Uber is already outside your house.** The driver is ready. You get in and go. That's a Lambda invocation on an already-initialized container.

### 15-Minute Timeout = Lambda's Timer
**15 min max timeout = Lambda's way of saying "keep it short!"** If your task takes longer, break it into pieces (Step Functions) or use a different service (Fargate/ECS).

> These memory tricks work because they **connect new info to things you already know**. Your brain loves patterns!

---

## How Lambda Works

### Lambda Lifecycle

```
Client -> API Gateway -> Lambda

If Cold Start:
  1. Download code
  2. Initialize runtime
  3. Run init code (Lambda function handler)

Then:
  4. Execute handler code
  5. Read/Write to DynamoDB
  6. Return response
```

### Cold Starts Explained

| Start Type | What Happens | Time |
|-----------|-------------|------|
| Cold Start | First invocation or after idle | 100ms - 10s+ |
| Warm Start | Subsequent invocations | 1-10ms |
| Frozen | After idle timeout | Cold start again |

**Tip:** Provisioned Concurrency keeps Lambda warm and ready to go!

---

## What is API Gateway?

API Gateway is a **fully managed service** that creates, publishes, and manages **APIs**. It acts as the **front door** for your Lambda functions, handling:

- **Request routing** to Lambda
- **Authentication and authorization**
- **Throttling and rate limiting**
- **API keys and usage plans**
- **Custom domains**
- **Caching**

### API Types

| Type | Use Case |
|------|----------|
| REST API | Traditional REST endpoints (most common) |
| HTTP API | Lightweight, cheaper, faster |
| WebSocket API | Real-time bidirectional communication |

---

## Key Features

### Lambda Features

| Feature | What It Does |
|---------|-------------|
| Max Timeout | 15 minutes |
| Max Memory | 128 MB to 10 GB |
| Deployment Package | Up to 250 MB (unzipped) |
| Layers | Share common code across functions |
| Supported Languages | Python, Node.js, Java, Go, C#, Ruby, and more |
| Environment Variables | Store config outside your code |
| Event Sources | 200+ AWS services can trigger Lambda |

### API Gateway Features

| Feature | What It Does |
|---------|-------------|
| Authorization | IAM, Cognito, Lambda authorizers |
| Throttling | Rate limiting (default 10,000 req/s) |
| Monitoring | CloudWatch metrics built-in |
| API Keys | Meter and charge for API usage |
| Custom Domains | Use your own domain name |
| Request/Response Transformation | Modify requests and responses |
| Caching | Cache responses to reduce Lambda calls |

---

## Architecture Overview

### Full Serverless Architecture

```
    +----------------+     +-------------------+     +------------------+
    |     Client     |     |   API Gateway     |     |  Lambda Functions|
    | Web/Mobile App |---->|  REST API         |---->|  getUser         |
    |                |     |  Throttling       |     |  createOrder     |
    |                |     |  Auth             |     |  processImage    |
    +----------------+     +-------------------+     +------------------+
                                                            |
                                                            v
                                                   +------------------+
                                                   |   Data Layer     |
                                                   | DynamoDB, S3     |
                                                   | RDS via Proxy    |
                                                   +------------------+
                                                            |
                                                            v
                                                   +------------------+
                                                   | Event Processing |
                                                   | SNS, SQS         |
                                                   | EventBridge      |
                                                   +------------------+
```

### How API Gateway + Lambda + DynamoDB Work Together

```
Client --[HTTP Request]--> API Gateway --[Invoke]--> Lambda --[Read/Write]--> DynamoDB
Client <--[HTTP Response]-- API Gateway <--[Response]-- Lambda
```

---

## Story Time: An S3-Triggered Lambda

Ravi, here's a real scenario that'll make this click.

You run an e-commerce site. Customers upload product photos. But the photos come in all sizes — some are 4K, some are phone snaps. You need them all to be 800x600 thumbnails for your catalog page.

**The old way:** You'd have a server running 24/7, polling S3 every few seconds for new images. Wasting money when nothing's uploaded. Slowing down when 50 images arrive at once.

**The Lambda way:**

1. Customer uploads `product-photo.jpg` to an S3 bucket.
2. S3 sends an **event notification** to Lambda: "Hey, a new file just landed."
3. Lambda spins up automatically. Downloads the image. Resizes it using Pillow or Sharp. Uploads the thumbnail to a `thumbnails/` folder in the same bucket.
4. Lambda shuts down. Total runtime: 300ms.

**What happened behind the scenes:**

- The upload triggered the Lambda (event-driven, not polling).
- Lambda scaled automatically — 10 uploads = 10 parallel Lambdas.
- You paid for 300ms of compute. Not an always-on server.
- No server to patch, no scaling to configure, no idle costs.

**And here's the kicker:** If 10,000 users upload photos during a flash sale, Lambda handles it. No queue. No "server overloaded." It just... works.

This is the power of event-driven, serverless architecture. S3 as the trigger. Lambda as the processor. No servers anywhere in the picture.

---

## Common Use Cases

| Use Case | Architecture | Why Lambda? |
|----------|-------------|-------------|
| REST API | API GW - Lambda - DynamoDB | No server management |
| File Processing | S3 - Lambda (resize image) | Event-driven, auto-scaling |
| Scheduled Tasks | EventBridge - Lambda (cron) | No always-on server needed |
| Chatbot | Lex - Lambda - DynamoDB | Pay per interaction |
| Data Pipeline | Kinesis - Lambda - S3 | Stream processing |
| Webhooks | API GW - Lambda - Process | Handle external callbacks |
| Microservices | API GW - Lambda - DB | Independent scaling per service |
| Email Processing | SES - Lambda - Action | React to incoming emails |

---

## Lambda Pricing

| Component | Cost |
|-----------|------|
| Free Tier | 1M requests/month, 400,000 GB-seconds |
| Requests | $0.20 per 1M requests |
| Duration | $0.0000166667 per GB-second |
| Provisioned Concurrency | $0.0000041667 per GB-second |

**Example:** A function that runs for 200ms with 256MB memory, 1M invocations/month:
- Cost: ~$0.20 (requests) + ~$0.33 (duration) = **~$0.53/month**

---

## Best Practices

| Practice | Why It Matters |
|----------|---------------|
| Keep functions small | Faster cold starts, easier debugging |
| Use Layers | Share code, reduce package size |
| Set proper timeouts | Prevent runaway executions |
| Use environment variables | No hardcoded secrets |
| Use Dead Letter Queues | Catch failed event processing |
| Monitor with CloudWatch | Track invocations, errors, duration |
| Do not log sensitive data | Security best practice |
| Set API Gateway throttling | Prevent abuse and cost overruns |
| Use connection pooling | Reuse DB connections (RDS Proxy) |
| Use Provisioned Concurrency | For latency-sensitive workloads |

---

## Common Mistakes

| Mistake | What Happens | Fix |
|---------|-------------|-----|
| Too much in one Lambda | Long cold starts, hard to debug | Split into smaller functions |
| Ignoring cold starts | Slow response times | Use Provisioned Concurrency |
| No Dead Letter Queue | Lost failed events | Configure DLQ for async invocations |
| Hardcoding secrets | Security risk | Use environment variables + Secrets Manager |
| Not setting memory | Poor performance | Right-size memory (more memory = more CPU) |
| No API Gateway throttling | Cost explosion from traffic spikes | Set rate and burst limits |
| Synchronous calls everywhere | Unnecessary cold starts | Use async invocations where possible |
| Not using Lambda Layers | Code duplication across functions | Extract shared code to layers |

---

## Interview Questions

### Q1: What is a cold start and how do you reduce it?

**Answer:** A **cold start** happens when Lambda initializes a new execution environment (first call or after idle). It includes downloading code, starting the runtime, and running init code. To reduce cold starts: use **Provisioned Concurrency**, keep deployment packages **small**, avoid heavy initialization code, and use **warmer functions** for critical paths.

**One-Liner:** Cold start = Uber driving to you from far away. Solution: Provisioned Concurrency = Uber parked outside your house.

### Q2: Lambda vs EC2 — when do you use which?

**Answer:** **Lambda** = event-driven, auto-scaling, 15 min max, you pay per invocation. Good for short tasks, APIs, file processing. **EC2** = full control, long-running, you manage the server. Good for databases, legacy apps, workloads that need hours of runtime.

**One-Liner:** Lambda = Uber (pay per ride, no maintenance). EC2 = Owning a car (full control, you maintain it).

### Q3: Does Lambda scale?

**Answer:** Yes, automatically! One invocation = one instance. 1000 requests = 1000 parallel Lambda instances. There's no limit by default, but you can set a **reserved concurrency** limit. Each invocation is independent and isolated.

**One-Liner:** 1 request = 1 instance. 1000 requests = 1000 instances. Automatic. No config needed.

### Q4: What is the difference between API Gateway REST API and HTTP API?

**Answer:** **REST API** has more features (caching, request validation, WAF integration, API keys) but costs more. **HTTP API** is lightweight, cheaper (about 70% less), and faster but has fewer features. Use HTTP API for simple Lambda integrations. Use REST API when you need advanced features like caching or WAF.

**One-Liner:** REST API = feature-rich but expensive. HTTP API = lightweight and 70% cheaper.

### Q5: Can Lambda connect to an RDS database? What are the challenges?

**Answer:** Yes, but Lambda creates a **new connection** on each invocation, which can exhaust RDS connections. Solution: Use **RDS Proxy** to pool connections. RDS Proxy sits between Lambda and RDS, maintaining a pool of reusable connections. This is critical for production serverless applications.

**One-Liner:** Yes. But Lambda opens a new DB connection each time. Use RDS Proxy to pool them.

### Q6: What is the maximum timeout for a Lambda function?

**Answer:** **15 minutes** (900 seconds). For longer-running tasks, use **Step Functions** to orchestrate multiple Lambda calls, or use **Fargate/ECS** for workloads that need more time. If your Lambda is hitting the timeout limit, it's usually a sign you should break the work into smaller pieces.

**One-Liner:** 15 minutes. Break it up or use Step Functions / Fargate.

---

## Think About It

Pause and reflect on these before moving on:

1. **If Lambda scales to 10,000 instances automatically, what happens to your DynamoDB table?** How do you prevent it from being overwhelmed?
2. **Why would you choose EC2 over Lambda?** Think of 3 real scenarios where Lambda's limitations make EC2 the better choice.
3. **You have a Lambda that processes payments. Cold starts cause timeouts.** What are your options? What's the tradeoff of each?
4. **Lambda charges per request AND per duration.** If you have a function that runs for 1 second with 1GB memory, and another that runs for 100ms with 128MB memory — which costs more? By how much?
5. **API Gateway has a 29-second timeout.** Your Lambda takes 35 seconds. What do you do? (Hint: think async.)

> These prompts force you to **think actively** rather than passively read. That's where real learning happens.

---

## Summary

| Service | Purpose |
|---------|---------|
| Lambda | Serverless compute, run code without servers |
| API Gateway | Front door for APIs, routes requests to Lambda |

Lambda + API Gateway is the **foundation of serverless architecture** on AWS. Together they let you build APIs, process events, and run code without managing a single server. Master them and you're well on your way to being a serverless superhero!

---

## Next Up: [20 - ECS, ECR and EKS](../20%20-%20ECS%2C%20ECR%20and%20EKS/README.md)

> Lambda is great, but sometimes you need containers! Let's learn about ECS, ECR, and EKS!
