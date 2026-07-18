# 03 — IAM (Identity and Access Management)

![Phase](https://img.shields.io/badge/Phase-1%20Foundations-blue?style=flat-square)
![Difficulty](https://img.shields.io/badge/Difficulty-⭐⭐%20Important-yellow?style=flat-square)
![Read%20Time](https://img.shields.io/badge/Read%20Time-~15%20min-yellow?style=flat-square)

> *"Ravi, before you touch ANY AWS service, you MUST understand IAM. It's the lock on your front door. Without it, anyone can walk in and rob you."* — **Rithu** 🔒

---

## The 80/20 Rule

> 🎯 **FOCUS HERE — This covers 80% of IAM interview questions:**
>
> 1. Users vs Roles
> 2. Least Privilege
> 3. MFA
> 4. Policy structure (EAR)
> 5. Groups
>
> Master these five things, and you can answer almost any IAM question thrown at you.

---

## What is IAM?

IAM (Identity and Access Management) is **AWS's security system**. It controls **who** can access **what** and **what they can do** with those resources.

---

## The Feynman Test

> 🧪 **Can you explain it simply?**
>
> IAM is like a bouncer at a club. The bouncer checks your ID (authentication), sees what VIP section you can access (authorization), and if you're not on the list, you're out.

---

## Why Do We Need It?

Without IAM:
- Anyone with your AWS credentials can do ANYTHING
- You can't control who does what
- No audit trail of who accessed what

With IAM:
- Each person gets their own login
- You control exactly what each person can do
- Everything is logged and auditable

---

## Real-World Analogy

> Think of IAM like an **office building:**
>
> - **User** = An employee with an ID card
> - **Group** = Department (Engineering, HR, Finance)
> - **Role** = Temporary access for a visitor or contractor
> - **Policy** = The rules written on the ID card ("Can enter floors 1-3, cannot enter server room")
> - **Root Account** = The building owner — has keys to EVERYTHING (use rarely!)

---

## How It Works

```
  ┌──────────────────────────────────────────┐
  │              IAM Components               │
  │                                          │
  │  ┌──────────┐     ┌──────────────────┐   │
  │  │  Users    │────▶│  Groups           │   │
  │  │ (People)  │     │ (Dev, Admin, etc) │   │
  │  └──────────┘     └────────┬─────────┘   │
  │                            │              │
  │                            ▼              │
  │                     ┌──────────────┐      │
  │                     │   Policies    │      │
  │                     │ (Permissions) │      │
  │                     └──────┬───────┘      │
  │                            │              │
  │                            ▼              │
  │                     ┌──────────────┐      │
  │                     │   AWS Resources│      │
  │                     │ (EC2, S3, etc) │      │
  │                     └──────────────┘      │
  └──────────────────────────────────────────┘
```

---

## Key Components

> 💡 **Memory Trick:** UGPR = User, Group, Policy, Role. Think: **"U Got Problems? Run!"** (to your admin) 🏃

### 1. Root Account

| Aspect | Detail |
|:-------|:-------|
| **What** | The email you signed up with |
| **Power** | Full access to EVERYTHING |
| **Risk** | If compromised = game over |
| **Rule** | Use ONLY for account setup. Then create an IAM Admin user. |

> 💡 **Memory Trick:** Root = **God mode.** Use once, then disable. 🎮

> *Rithu: "Using root for daily tasks is like driving a tank to buy groceries. Technically possible, but WHY?"* 🪖🛒

---

### 2. IAM Users

| Aspect | Detail |
|:-------|:-------|
| **What** | Individual login credentials |
| **Use For** | People who need AWS access |
| **Best Practice** | One user per person, MFA enabled |

---

### 3. IAM Groups

| Aspect | Detail |
|:-------|:-------|
| **What** | Collection of users |
| **Use For** | Apply same policy to multiple users |
| **Example** | "Developers" group → all devs get same EC2 access |

```
  ┌─────────────────────────────┐
  │        IAM Groups           │
  │                             │
  │  👨‍💻 Dev Group:              │
  │    ├── Ravi                 │
  │    ├── Priya                │
  │    └── Amit                 │
  │    Policy: EC2-ReadOnly     │
  │                             │
  │  👔 Admin Group:            │
  │    ├── Rithu                │
  │    └── Manager              │
  │    Policy: AdminAccess      │
  └─────────────────────────────┘
```

---

### 4. IAM Roles

| Aspect | Detail |
|:-------|:-------|
| **What** | Temporary permissions for AWS services or users |
| **Use For** | EC2 accessing S3, Lambda accessing DynamoDB |
| **Key Idea** | No credentials stored — AWS handles the auth |

> *Rithu: "Roles are like visitor passes. You don't give your house key to a guest — you let them in for a specific visit and then the pass expires."* 🎫

---

### 5. IAM Policies

| Aspect | Detail |
|:-------|:-------|
| **What** | JSON documents that define permissions |
| **Attached To** | Users, Groups, or Roles |
| **Format** | JSON with Effect, Action, Resource |

> 💡 **Memory Trick:** EAR = Effect, Action, Resource. Like an EAR on your head — it **hears** what's allowed. 👂

**Example Policy — Allow S3 read access:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

| Policy Element | Meaning |
|:---------------|:--------|
| `Effect` | Allow or Deny |
| `Action` | What action is allowed (e.g., s3:GetObject) |
| `Resource` | Which resource (e.g., specific S3 bucket) |

---

## Active Recall

> 🧠 **Think:** If you attach a policy to a Group, and add a User to that Group, does the User get the policy permissions?
>
> **Answer:** Yes! Users inherit all permissions from the Groups they belong to. That's the whole point of Groups — write the permission once, apply it to many.

> 🧠 **Think:** Can an IAM Role be assumed by both an EC2 instance AND a user?
>
> **Answer:** Yes! Roles are flexible. A Role's trust policy defines WHO can assume it — it could be an EC2 instance, a Lambda function, another AWS account, or even an IAM user.

---

## Least Privilege

> 💡 **Memory Trick:** Give someone a **spoon**, not the **whole kitchen.** 🥄🍳

---

## Key Features

| Feature | What It Means |
|:--------|:-------------|
| **MFA (Multi-Factor Authentication)** | Extra login step — password + phone code |
| **Password Policies** | Enforce complexity, rotation |
| **Access Keys** | For CLI/API access (not for console login) |
| **IAM Credentials Report** | Shows status of all users' credentials |
| **Policy Simulator** | Test if a policy allows/denies an action |

---

## Best Practices

1. **Enable MFA on root** — Non-negotiable!
2. **Create an Admin IAM user** — Stop using root after setup
3. **Use least privilege** — Give minimum permissions needed
4. **Use Groups** — Don't attach policies to individual users
5. **Rotate access keys** every 90 days
6. **Never hardcode credentials** in code or scripts
7. **Use IAM Roles** for EC2/Lambda instead of access keys

---

## Common Mistakes

| Mistake | Why It's Bad | Fix |
|:--------|:------------|:----|
| Using root for daily work | One mistake = full account compromise | Create IAM Admin user |
| No MFA on root | Password alone = easy target | Enable MFA immediately |
| `*` (wildcard) permissions | Gives access to EVERYTHING | Use specific resource ARNs |
| Sharing credentials | No accountability | One user per person |
| Hardcoding access keys in code | Keys exposed in Git repos | Use IAM Roles or environment variables |

---

## Story Time

> 📖 **Ravi starts a company:**
>
> He creates his AWS account (root). First thing? **Enable MFA on root.** Then create an **Admin IAM user.** Now he creates IAM Groups:
>
> - **"Developers"** — can use EC2 and S3
> - **"DBA"** — can use RDS
>
> Each employee gets an IAM User added to the right Group. His CI/CD pipeline uses an **IAM Role** to deploy. **Nobody has root access.** That's IAM done right!

---

## Interview Questions

1. **What is IAM?**
   > AWS service that controls who can access what and what actions they can perform on AWS resources.
   >
   > 🏷️ **One-Liner:** "IAM is AWS's identity and access control system — it answers WHO can do WHAT on WHICH resources."

2. **What is the difference between a User, Group, Role, and Policy?**
   > - **User:** Individual person with credentials
   > - **Group:** Collection of users with same permissions
   > - **Role:** Temporary permissions for services/users (no long-term credentials)
   > - **Policy:** JSON document defining what is allowed/denied
   >
   > 🏷️ **One-Liner:** "Users are people, Groups bundle people, Roles are temporary, Policies are the rules."

   > ⚠️ **INTERVIEW TRAP:** "What's the difference between IAM User and IAM Role?"
   >
   > Users have **permanent credentials** (access keys). Roles provide **temporary credentials.** Roles are for EC2 accessing S3, Lambda accessing DynamoDB, etc.
   >
   > 🏷️ **One-Liner:** "Users = permanent keys in your pocket. Roles = temporary visitor passes."

3. **What is least privilege?**
   > Giving a user/role only the minimum permissions needed to do their job, nothing more.
   >
   > 🏷️ **One-Liner:** "Minimum permissions needed — nothing more, nothing less."

   > ⚠️ **INTERVIEW TRAP:** "What is least privilege?"
   >
   > Don't just say "read-only." Be specific: "Give minimum permissions needed. Not 'read-only' when they need 'read-only-S3-bucket-specific-bucket'."
   >
   > 🏷️ **One-Liner:** "Don't give a spoon-maker access to the whole kitchen."

4. **Why should you not use the root account?**
   > Root has unrestricted access to everything. Using it for daily tasks creates unnecessary risk. One compromise = full account takeover.
   >
   > 🏷️ **One-Liner:** "Root is god mode — use once for setup, then lock it away forever."

   > ⚠️ **INTERVIEW TRAP:** "Should you use root for daily tasks?"
   >
   > **NEVER.** Create an IAM Admin user. Root is for account setup only.
   >
   > 🏷️ **One-Liner:** "Root is for setup, not for daily use — ever."

5. **What is MFA and why is it important?**
   > Multi-Factor Authentication adds a second layer of security (password + code from phone). Even if password is stolen, MFA prevents unauthorized access.
   >
   > 🏷️ **One-Liner:** "MFA = password + phone code = double the lock."

---

## Summary

| Component | Purpose |
|:----------|:--------|
| **Root Account** | Setup only — then lock it away |
| **Users** | Individual logins |
| **Groups** | Share permissions across users |
| **Roles** | Temporary access for services |
| **Policies** | JSON permission documents |
| **MFA** | Extra security layer |
| **Least Privilege** | Give minimum access |

---

**Next:** [04 — AWS CLI](../04%20-%20AWS%20CLI/) → *Talk to AWS from your terminal like a pro*

---

> *"Ravi, IAM is the first thing you set up and the last thing you forget. It's like putting on a seatbelt — boring, but it saves lives."* — **Rithu** 🚗
