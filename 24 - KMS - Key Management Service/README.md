# 🔑 24 - KMS (Key Management Service)

![Phase](https://img.shields.io/badge/Phase-10%20Governance-gray)
![Difficulty](https://img.shields.io/badge/Difficulty-%E2%AD%90%E2%AD%90%20Medium-yellow)
![Read Time](https://img.shields.io/badge/Read%20Time-~10%20min-blue)

> **Rithu:** "Ravi, last time we talked about security best practices. Now let's zoom into the **encryption engine** that powers almost all of AWS encryption — **KMS**!"
>
> **Ravi:** "Encryption sounds complicated..."
>
> **Rithu:** "KMS makes it surprisingly simple. Think of it as your personal **vault manager** in the cloud!"

---

## 📑 Table of Contents

- [What is KMS?](#-what-is-kms)
- [Why Do We Need KMS?](#-why-do-we-need-kms)
- [Real-World Analogy](#-real-world-analogy-)
- [Key Concepts](#-key-concepts)
- [Key Types](#-key-types)
- [How It Works — Envelope Encryption](#-how-it-works--envelope-encryption)
- [KMS Integrations](#-kms-integrations)
- [Architecture Overview](#-architecture-overview)
- [Common Use Cases](#-common-use-cases)
- [Best Practices](#-best-practices-)
- [Common Mistakes](#-common-mistakes-)
- [Interview Questions](#-interview-questions-)
- [Summary](#-summary)

---

## 🌟 What is KMS?

**AWS KMS (Key Management Service)** is a managed service that lets you **create, store, rotate, and control** encryption keys used to encrypt your data.

It's not a tool to encrypt data directly — it's the **manager of the keys** that do the encrypting.

## Why Do We Need KMS?

| Problem | KMS Solution |
|---------|-------------|
| "Where do I store encryption keys safely?" | KMS stores them in hardware security modules (HSMs) |
| "How do I rotate keys without breaking anything?" | Automatic key rotation |
| "How do I control who can encrypt/decrypt?" | IAM policies + key policies |
| "How do I prove compliance?" | Full audit trail via CloudTrail |

> **Without KMS:** You'd manage encryption keys yourself — scary and risky.
> **With KMS:** AWS handles the hard parts; you just decide WHO can use the keys.

---

## 🏦 Real-World Analogy

> Imagine a **bank vault**:
>
> - The **vault master key** (CMK) never leaves the bank — it's stored in a super-secure vault within the vault
> - When you need to lock your safe, the bank manager **creates a copy of a key** (data key) for you
> - You use that copy key to lock/unlock your safe
> - If the copy key is lost, the manager can make a new one
> - The original master key is **never exposed** — it stays protected
>
> **That's exactly how KMS works!**

---

## 🔑 Key Concepts

| Concept | What It Is |
|---------|-----------|
| **CMK (Customer Master Key)** | The master key that protects data keys. Never used directly on data. |
| **Data Key** | The key that actually encrypts/decrypts your data |
| **Envelope Encryption** | Encrypting the data key with the CMK, then using the data key on data |
| **Key Policy** | IAM-like policy that controls WHO can use the CMK |
| **Key Rotation** | Automatically creating new backing key material annually |

### The Key Hierarchy (Simplified)

```
┌──────────────────────┐
│   CMK (Master Key)   │  ← Stored in AWS HSM, never leaves
│   Protects: data keys │
└──────────┬───────────┘
           │ encrypts
           ▼
┌──────────────────────┐
│    Data Key          │  ← Used to encrypt actual data
│    Protects: your data│
└──────────┬───────────┘
           │ encrypts
           ▼
┌──────────────────────┐
│   Your Actual Data   │  ← S3 objects, EBS volumes, etc.
└──────────────────────┘
```

---

## 🏷️ Key Types

| Type | Who Manages It | Cost | Use Case |
|------|---------------|------|----------|
| **AWS-Managed Key** | AWS (automatic) | Free | Default encryption for AWS services |
| **Customer-Managed Key (CMK)** | You | $1/month + API calls | Compliance, custom policies |
| **AWS-Owned Key** | AWS (internal) | Free | Used by AWS services internally |

### Quick Comparison

```
AWS-Managed Key (aws/s3)
  → You CAN'T modify the policy
  → Free, auto-rotated
  → Good for: "I just need encryption"

Customer-Managed Key (my-app-key)
  → You CONTROL the policy
  → $1/month, you set rotation
  → Good for: "I need compliance + audit"

AWS-Owned Key
  → Internal to AWS
  → You can't even see it
  → Good for: you don't choose it — AWS uses it internally
```

> **Ravi:** "So if I just want basic encryption, the free one is fine?"
>
> **Rithu:** "Exactly! Use AWS-managed keys for simplicity. Use customer-managed keys when you need **control or compliance**."

---

## 📬 How It Works — Envelope Encryption

This is the **#1 concept** to understand about KMS.

### Step by Step

```
1. You call KMS: "Generate me a data key for my app"
                    │
                    ▼
2. KMS generates TWO things:
   ✅ Plaintext data key (the actual key)
   ✅ Encrypted data key (same key, encrypted by CMK)
                    │
                    ▼
3. You store the ENCRYPTED data key alongside your data
                    │
                    ▼
4. You use the PLAINTEXT data key to encrypt your data
                    │
                    ▼
5. You DELETE the plaintext data key from memory
                    │
                    ▼
6. When you need to DECRYPT:
   → Send encrypted data key to KMS
   → KMS decrypts it with the CMK
   → KMS returns plaintext data key
   → You use it to decrypt your data
```

### Why This Way?

| Reason | Explanation |
|--------|------------|
| **Performance** | CMK is slow for large data; data keys are fast |
| **Security** | CMK is never exposed — stays in HSM |
| **Scalability** | One CMK can protect millions of data keys |

> **Rithu:** "Envelope encryption is like a **locked envelope** — the letter (data) is inside, and the envelope is locked with a special seal (CMK). The postman (KMS) never reads the letter!"

---

## 🔌 KMS Integrations

KMS works with almost **everything** in AWS:

| Service | How KMS Is Used |
|---------|----------------|
| **S3** | SSE-KMS encryption for buckets |
| **EBS** | Encrypted volumes |
| **RDS** | Encrypted databases |
| **Lambda** | Encrypted environment variables |
| **Secrets Manager** | Encrypts secrets (uses KMS under the hood) |
| **CloudWatch Logs** | Encrypt log groups |
| **EFS** | Encrypted file systems |
| **Redshift** | Encrypted data warehouses |
| **SQS/SNS** | Encrypted queues and topics |

> **Ravi:** "Wait — Secrets Manager uses KMS too?"
>
> **Rithu:** "Yes! Secrets Manager is like a **wrapper** around KMS. KMS is the real encryption engine!"

---

## 🏗️ Architecture Overview

```
                    ┌──────────────────┐
                    │     IAM User     │
                    │  (needs access)  │
                    └────────┬─────────┘
                             │
                    ┌────────▼─────────┐
                    │    KMS Key       │
                    │  Policy allows   │
                    │  this user       │
                    └────────┬─────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
    ┌─────────▼───┐  ┌──────▼──────┐  ┌───▼─────────┐
    │ S3 Bucket   │  │ EBS Volume  │  │ RDS Database │
    │ SSE-KMS     │  │ Encrypted   │  │ Encrypted    │
    └─────────────┘  └─────────────┘  └──────────────┘
              │              │              │
              └──────────────┼──────────────┘
                             │
                    ┌────────▼─────────┐
                    │   CloudTrail     │
                    │  (audit every    │
                    │   encrypt/decrypt)│
                    └──────────────────┘
```

---

## 🎯 Common Use Cases

| Use Case | How KMS Helps |
|----------|--------------|
| **Encrypt S3 data** | Enable SSE-KMS on bucket |
| **Encrypt EBS volumes** | Check "encrypted" when creating volume |
| **Encrypt database** | Enable encryption on RDS at launch |
| **Protect secrets** | Secrets Manager uses KMS |
| **Encrypt backups** | Snapshots inherit KMS encryption |
| **Code signing** | Sign Lambda deployments with KMS |
| **Custom authentication** | Generate/verify HMAC signatures |

---

## ✅ Best Practices

| Practice | Why |
|----------|-----|
| Use **AWS-managed keys** for simplicity | Free, auto-rotated, no management |
| Use **customer-managed keys** for compliance | Full control, audit, custom policies |
| **Enable automatic key rotation** | CMK material rotates yearly (free) |
| **Use key policies** to restrict access | Don't rely on IAM alone |
| **Monitor with CloudTrail** | Track every encrypt/decrypt call |
| **Use grants** for temporary access | Better than modifying key policies |
| **Separate keys per environment** | Dev key ≠ Prod key |

---

## ❌ Common Mistakes

| Mistake | What Happens |
|---------|-------------|
| **Not encrypting data at rest** | Data exposed if storage is compromised |
| **Hardcoding encryption keys** | Keys in source code = instant breach |
| **Not understanding KMS costs** | $1/month per CMK + $0.03 per 10K API calls |
| **Using root account for KMS** | Violates least privilege |
| **Not enabling CloudTrail** | No audit trail for compliance |
| **Forgetting KMS is regional** | Keys don't cross regions automatically |

> **Ravi:** "So KMS keys are region-specific? If I copy an encrypted snapshot to another region..."
>
> **Rithu:** "The data key gets re-encrypted with a KMS key in the destination region automatically. But you need KMS access in both regions!"

---

## 🎤 Interview Questions

### Q1: What is envelope encryption?
> **Answer:** Envelope encryption means encrypting your data with a **data key**, and then encrypting that data key with a **CMK in KMS**. The CMK never directly touches your data. This is fast (data keys are fast) and secure (CMK stays protected in HSM).

### Q2: What is the difference between AWS-managed and customer-managed keys?
> **Answer:** AWS-managed keys are free, auto-rotated, but you can't modify their policies. Customer-managed keys cost $1/month, you control policies and rotation, and they're needed for compliance requirements. Both use the same underlying HSM security.

### Q3: Can you use KMS keys across regions?
> **Answer:** No, KMS keys are **regional**. However, you can use **multi-region keys** (a newer feature) that replicate a CMK to another region. For most cases, KMS automatically re-encrypts data keys when you copy encrypted data cross-region.

### Q4: Why not just encrypt data directly with the CMK?
> **Answer:** CMKs are stored in HSMs and are slow for large data. Envelope encryption lets you use the CMK to protect **data keys**, which are fast symmetric keys that handle bulk encryption. One CMK can protect millions of data keys.

### Q5: How does KMS integrate with S3?
> **Answer:** When you enable **SSE-KMS** on an S3 bucket, every `PUT` object triggers KMS to generate/encrypt a data key. The encrypted data key is stored with the object. On `GET`, KMS decrypts the data key, and S3 uses it to decrypt the object. All KMS calls are logged in CloudTrail.

---

## 📝 Summary

| Concept | Key Takeaway |
|---------|-------------|
| **CMK** | Master key — never touches data directly |
| **Data Key** | The key that actually encrypts your data |
| **Envelope Encryption** | CMK encrypts data key; data key encrypts data |
| **AWS-Managed** | Free, simple, auto-rotated |
| **Customer-Managed** | $1/month, full control, compliance |
| **Integrations** | S3, EBS, RDS, Lambda, Secrets Manager, and more |
| **Regional** | Keys don't cross regions (use multi-region keys) |

> **Ravi:** "KMS is actually the backbone of so many AWS services — I had no idea!"
>
> **Rithu:** "It really is! And speaking of backbone — let's talk about how to **protect all your data** with AWS Backup! 🛡️"

---

## ➡️ Next: [25 - AWS Backup](../25%20-%20AWS%20Backup/README.md)
