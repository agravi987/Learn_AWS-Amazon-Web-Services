# 04 — AWS CLI (Command Line Interface)

![Phase](https://img.shields.io/badge/Phase-1%20Foundations-blue?style=flat-square)
![Difficulty](https://img.shields.io/badge/Difficulty-⭐%20Beginner-brightgreen?style=flat-square)
![Read%20Time](https://img.shields.io/badge/Read%20Time-~10%20min-yellow?style=flat-square)

> *"Ravi, the AWS Console is great for learning. But real DevOps engineers use the CLI. It's faster, scriptable, and looks way cooler in interviews."* — **Rithu** 😎

---

## AWS CLI Architecture Overview

![AWS CLI Architecture](http://docs.aws.amazon.com/cli/latest/userguide/images/cli-architecture.png)

> *AWS CLI translates commands into API calls that interact with AWS services.*

---

## What is AWS CLI?

AWS CLI is a **command-line tool** that lets you manage AWS services from your terminal instead of the browser console.

---

## Why Do We Need It?

- **Speed:** Type a command vs. clicking through 10 pages
- **Automation:** Script repetitive tasks (backup, deploy, monitor)
- **CI/CD:** Pipelines use CLI, not browsers
- **Reproducibility:** Same command = same result, every time

---

## Real-World Analogy

> - **AWS Console** = Driving a car with a steering wheel (visual, manual)
> - **AWS CLI** = Driving a car with autopilot (programmatic, automated)
>
> Both get you there. But one lets you do it 100 times without getting tired.

---

## How It Works

```
  Your Terminal
       │
       │ aws s3 ls
       ▼
  AWS CLI translates command → API call
       │
       ▼
  AWS API receives request → Processes it
       │
       ▼
  Response comes back to your terminal
```

**Example:**

```bash
# List all S3 buckets (same as clicking "S3" in console)
aws s3 ls

# Create a new EC2 instance (instead of clicking through 10 pages)
aws ec2 run-instances --image-id ami-12345 --instance-type t2.micro
```

---

## Key Features

| Feature | What It Means |
|:--------|:-------------|
| **Install on any OS** | Windows, Mac, Linux |
| **Works with all AWS services** | S3, EC2, IAM, Lambda — everything |
| **Scriptable** | Write bash/batch scripts with AWS commands |
| **Output formats** | Text, JSON, YAML, table |
| **Auto-completion** | Tab completion for commands |

---

## Setup Steps

### 1. Install CLI

```bash
# Windows (using pip)
pip install awscli

# Mac
brew install awscli

# Linux
sudo apt install awscli
```

### 2. Configure Credentials

```bash
aws configure
```

It will ask for:
- **AWS Access Key ID** — from IAM user
- **AWS Secret Access Key** — from IAM user
- **Default Region** — e.g., `ap-south-1`
- **Default Output Format** — `json`

### 3. Verify

```bash
aws sts get-caller-identity
```

This returns your current IAM user info — confirms CLI is working.

---

## Common Commands Cheat Sheet

### S3 (Storage)

```bash
aws s3 ls                          # List all buckets
aws s3 ls s3://my-bucket           # List files in bucket
aws s3 cp file.txt s3://my-bucket  # Upload file
aws s3 cp s3://my-bucket/file.txt . # Download file
aws s3 mb s3://new-bucket-name     # Create bucket
aws s3 rb s3://bucket-name --force # Delete bucket
```

### EC2 (Compute)

```bash
aws ec2 describe-instances         # List all instances
aws ec2 start-instances --instance-ids i-12345  # Start
aws ec2 stop-instances --instance-ids i-12345   # Stop
aws ec2 terminate-instances --instance-ids i-12345 # Terminate
```

### IAM (Security)

```bash
aws iam list-users                 # List all IAM users
aws iam create-user --user-name ravi  # Create user
```

---

## Best Practices

1. **Never hardcode credentials** — use `aws configure` or IAM Roles
2. **Use profiles** for multiple accounts — `aws --profile production s3 ls`
3. **Use `--dry-run`** to test commands before executing
4. **Combine with scripts** — automate backups, deployments, cleanup
5. **Use JSON output** for parsing — `--output json`

---

## Common Mistakes

| Mistake | Why It's Bad | Fix |
|:--------|:------------|:----|
| Hardcoding access keys in scripts | Security risk if script is shared | Use `aws configure` or IAM Roles |
| Running as root user | Full account access | Create IAM user for CLI |
| No region specified | Command fails or runs in wrong region | Set default region in `aws configure` |
| Not using `--dry-run` | Accidentally deleting production resources | Always test first |

---

## Interview Questions

1. **What is AWS CLI?**
   > A command-line tool to manage AWS services programmatically. It's faster and more automatable than the console.

2. **How do you configure AWS CLI?**
   > Run `aws configure` and provide Access Key ID, Secret Access Key, default region, and output format.

3. **How is CLI different from Console?**
   > Console is browser-based (manual). CLI is terminal-based (scriptable). CLI is preferred for automation and CI/CD.

4. **What is the benefit of using CLI in DevOps?**
   > Scripts can be version-controlled, shared, and automated. Same command = same result every time.

5. **What is `aws sts get-caller-identity` used for?**
   > It returns the current IAM user/role's identity. Used to verify CLI is configured correctly.

---

## Summary

| Concept | Key Point |
|:--------|:----------|
| **What** | Command-line tool for AWS |
| **Why** | Speed, automation, CI/CD compatibility |
| **Setup** | Install → `aws configure` → use commands |
| **Security** | Never hardcode credentials |
| **Pro Tip** | Learn the most common 10 commands well |

---

**Next:** [05 — EC2](../05%20-%20EC2%20-%20Elastic%20Compute%20Cloud/) → *Virtual servers — the backbone of AWS*

---

> *"Ravi, the CLI is like the command line in The Matrix. Once you see it, you can't go back to clicking buttons."* — **Rithu** 🔴🔵
