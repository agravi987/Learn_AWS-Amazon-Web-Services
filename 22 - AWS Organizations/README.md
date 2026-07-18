# 🏢 AWS Organizations — Multi-Account Management

![Phase](https://img.shields.io/badge/Phase--10-Governance-gray)
![Difficulty](https://img.shields.io/badge/Difficulty-%E2%AD%90%E2%AD%90-Medium-yellow)
![Read Time](https://img.shields.io/badge/Read%20Time-~10%20min-blue)

> **Rithu:** "Ravi, imagine you run a company with 5 departments — each one needs its own AWS account. How do you manage billing, permissions, and security across ALL of them without losing your mind?"
>
> **Ravi:** "Uhh... 5 logins? 😅"
>
> **Rithu:** "Nope! AWS **Organizations** lets you manage everything from ONE place. Think of it as the CEO of your AWS accounts! 👔"

---

## AWS Organizations Architecture Overview

![AWS Organizations Architecture](http://docs.aws.amazon.com/organizations/latest/userguide/images/org-structure.png)

> *AWS Organizations hierarchy with management account, OUs, and member accounts.*

---

## What Is AWS Organizations?

**AWS Organizations** is a service that lets you **manage multiple AWS accounts** from a central place.

| Concept | What It Is |
|---------|-----------|
| **Management Account** | The parent account — the boss 👑 |
| **Member Accounts** | Child accounts managed by the org |
| **Organizational Unit (OU)** | A group of accounts (like departments) |
| **Service Control Policy (SCP)** | Permission guardrails for accounts |
| **Consolidated Billing** | One bill for all accounts |

---

## 🤔 Why Do We Need It?

Running everything in one AWS account is a recipe for disaster:

| Problem | What Happens |
|---------|-------------|
| One account for everything | Dev team accidentally deletes prod database 💀 |
| No billing separation | Can't tell who's spending what |
| Can't enforce policies | One person's mistake affects everyone |
| Security nightmare | Everyone has broad permissions |

**Organizations solves this** by giving you:

- **Separation of concerns** — separate accounts for dev, staging, prod
- **Centralized control** — one place to manage all accounts
- **Consolidated billing** — one bill, volume discounts
- **Permission boundaries** — SCPs limit what accounts can do

---

## 🏢 Real-World Analogy

> **AWS Organizations is like a holding company** 🏢

> Imagine a parent company (Management Account) that owns several subsidiary companies (Member Accounts):
>
> - 🏭 **Dev Corp** — builds new products
> - 🧪 **Staging Inc** — tests everything
> - 🚀 **Prod LLC** — runs production services
> - 💰 **Finance Co** — handles billing & budgets
>
> The parent company sets **rules** (SCPs): "No subsidiary can delete S3 buckets" or "All accounts must use MFA." Each subsidiary operates independently, but under the parent's governance.

---

## ⚙️ How AWS Organizations Works

### Account Structure

```
                    ┌─────────────────────┐
                    │  Management Account │
                    │     (The Boss 👑)    │
                    └──────────┬──────────┘
                               │
            ┌──────────────────┼──────────────────┐
            │                  │                   │
            ▼                  ▼                   ▼
    ┌──────────────┐  ┌──────────────┐   ┌──────────────┐
    │  Dev OU      │  │  Staging OU  │   │  Prod OU     │
    │              │  │              │   │              │
    │ ┌──────────┐ │  │ ┌──────────┐ │   │ ┌──────────┐ │
    │ │ Dev Acct │ │  │ │ Stg Acct│ │   │ │ Prod Acct│ │
    │ └──────────┘ │  │ └──────────┘ │   │ └──────────┘ │
    │ ┌──────────┐ │  │              │   │              │
    │ │ Test Acct│ │  │              │   │              │
    │ └──────────┘ │  │              │   │              │
    └──────────────┘  └──────────────┘   └──────────────┘
```

---

## 🔒 Service Control Policies (SCPs)

SCPs are the **most powerful feature** of Organizations. They act as permission **guardrails**.

> **Analogy:** SCPs are like a speed limit sign on a road. Even if a driver (IAM policy) wants to go 150 mph, the speed limit (SCP) says "max 60." The driver can't exceed it.

### How SCPs Work

```
┌─────────────────────────────────────────────────┐
│              Permission Hierarchy                 │
│                                                   │
│  SCP (Organization boundary)                     │
│    └──▶ IAM Policy (Account level)               │
│          └──▶ IAM Policy (User/Role level)       │
│                                                   │
│  ALL THREE must allow the action                 │
│  If ANY denies, the action is DENIED             │
└─────────────────────────────────────────────────┘
```

### Common SCP Examples

| SCP | What It Prevents |
|-----|-----------------|
| Deny leaving organization | Members can't create independent accounts |
| Deny root user access | Prevents root user usage in member accounts |
| Require MFA | Forces multi-factor authentication |
| Restrict regions | Only allow resources in specific regions |
| Deny S3 public access | Prevents making S3 buckets public |
| Deny disabling CloudTrail | Ensures logging can't be turned off |

> ⚠️ **Important:** SCPs affect **all users and roles** in the account, including root. They don't affect the Management Account.

---

## 🎯 Common Use Cases

| Use Case | How Organizations Helps |
|----------|------------------------|
| Enterprise multi-account strategy | Separate accounts per team/project |
| Environment separation | Dev → Staging → Prod in different accounts |
| Compliance & auditing | Centralized logging, enforced policies |
| Cost management | Track spending per account, set budgets |
| Security isolation | Blast radius limited to one account |
| Merger & acquisition | Quickly add new accounts to the org |
| Education / labs | Create sandbox accounts safely |

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    Management Account                     │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌───────────┐ │
│  │ Billing  │ │CloudTrail│ │ IAM      │ │SCP Mgmt  │ │
│  └──────────┘ └──────────┘ └──────────┘ └───────────┘ │
└────────────────────────┬────────────────────────────────┘
                         │
         ┌───────────────┼───────────────┐
         │               │               │
         ▼               ▼               ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│  Dev Account │ │ Stage Account│ │ Prod Account │
│              │ │              │ │              │
│ SCPs apply ──┤ │ SCPs apply ──┤ │ SCPs apply ──┤
│ IAM roles   │ │ IAM roles   │ │ IAM roles   │
│ EC2, S3, RDS│ │ EC2, S3, RDS│ │ EC2, S3, RDS│
└──────────────┘ └──────────────┘ └──────────────┘
         │               │               │
         └───────────────┼───────────────┘
                         ▼
              ┌──────────────────┐
              │ Consolidated Bill │
              │    (One Bill)     │
              └──────────────────┘
```

---

## 💰 Consolidated Billing

One of the best perks! All accounts share a single bill.

| Benefit | How |
|---------|-----|
| **Volume discounts** | Combined usage = lower prices (e.g., S3, data transfer) |
| **Cost tracking** | See costs broken down by account |
| **Payment** | One payment method for all accounts |
| **Reserved Instances** | Shared across the entire organization |
| **Savings Plans** | Automatically applied across accounts |

> **Rithu:** "Think of it like a family phone plan — everyone gets their own line, but the family discount applies to all of you! 📱"

---

## ✅ Best Practices

| Practice | Why |
|----------|-----|
| **Separate dev/staging/prod accounts** | Limit blast radius, cost tracking |
| **Use SCPs to restrict actions** | Prevent dangerous operations across accounts |
| **Enable CloudTrail in management account** | Audit who did what and when |
| **Use consolidated billing** | Volume discounts, easier payment |
| **Don't use management account for daily work** | It's too powerful — use member accounts |
| **Apply SCP early** | Set guardrails before creating member accounts |
| **Use AWS SSO (IAM Identity Center)** | Single sign-on across all accounts |
| **Set up budget alerts per account** | Catch spending surprises early |

---

## ❌ Common Mistakes

| Mistake | Impact |
|---------|--------|
| Using management account for daily work | High risk — it has unrestricted access |
| Not applying SCPs | No guardrails = potential chaos |
| Sharing accounts between teams | Can't track costs or permissions |
| Forgetting to enable CloudTrail | No audit trail for compliance |
| Not using consolidated billing | Missing volume discounts 💸 |
| Creating too many accounts without governance | Organizational sprawl |
| Giving member accounts admin access to management account | Security risk |

---

## 🎤 Interview Questions

### Q1: What is the difference between the Management Account and Member Accounts?
**Answer:** The **Management Account** is the root/parent account that owns the organization — it controls billing, can create member accounts, and manages SCPs. **Member Accounts** are child accounts managed by the organization. The management account is NOT affected by SCPs, so it has full access.

### Q2: What are Service Control Policies (SCPs) and how do they work?
**Answer:** SCPs are permission guardrails applied at the OU or account level. They define the MAXIMUM permissions for all IAM users and roles in that account. If an SCP denies an action, no IAM policy in that account can override it. SCPs don't affect the management account.

### Q3: Can you move an existing AWS account into an Organization?
**Answer:** Yes! You can invite an existing account to join your organization. The existing account owner must accept the invitation. Once joined, SCPs and consolidated billing apply. Note: an account can only belong to one organization at a time.

### Q4: What is the benefit of consolidated billing in Organizations?
**Answer:** Consolidated billing provides one bill for all accounts, enables **volume discounts** (combined usage = lower per-unit costs), allows **Reserved Instance sharing** across accounts, and simplifies payment management. It's like a family plan — everyone benefits from the combined usage.

### Q5: What happens if a Member Account tries to leave the Organization?
**Answer:** A member account can request to leave, but the management account must approve it. Once it leaves, it loses access to any organization-level resources (like shared RDS snapshots) and is billed independently. SCPs no longer apply.

---

## 📝 Summary

```
┌─────────────────────────────────────────────────────┐
│           AWS Organizations Recap                     │
├─────────────────────────────────────────────────────┤
│ Management Account = The Boss (parent account)      │
│ Member Accounts    = Child accounts (teams/envs)    │
│ OUs                = Groups of accounts             │
│ SCPs               = Permission guardrails          │
├─────────────────────────────────────────────────────┤
│ ✅ Centralized multi-account management             │
│ ✅ Consolidated billing & volume discounts           │
│ ✅ SCPs enforce security policies organization-wide  │
│ ✅ Separation of concerns (dev/staging/prod)         │
├─────────────────────────────────────────────────────┤
│ ⚠️  Don't use management account for daily work      │
│ ⚠️  Always apply SCPs to member accounts            │
│ ⚠️  Enable CloudTrail for auditing                  │
└─────────────────────────────────────────────────────┘
```

> **Ravi:** "So Organizations is like having a parent company that manages all the subsidiaries — each one is independent, but the parent sets the rules?"
>
> **Rithu:** "Perfect! And those rules (SCPs) make sure nobody accidentally blows up the entire company. 🏢💥 Now let's wrap up with security, cost optimization, and more interview prep!"
>
> **Ravi:** "Let's go! 🚀"

---

**Next:** [23 - Security, Cost Optimization and Interview Q&A →](../23%20-%20Security,%20Cost%20Optimization%20and%20Interview%20Q%26A/README.md)
