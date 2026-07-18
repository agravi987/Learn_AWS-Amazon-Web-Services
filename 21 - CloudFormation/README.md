# 🏗️ AWS CloudFormation — Infrastructure as Code

![Phase](https://img.shields.io/badge/Phase--9-Infrastructure%20as%20Code-pink)
![Difficulty](https://img.shields.io/badge/Difficulty-%E2%AD%90%E2%AD%90%E2%AD%90-Hard-red)
![Read Time](https://img.shields.io/badge/Read%20Time-~12%20min-blue)

> **Rithu:** "Ravi, what if I told you that you could describe your entire AWS infrastructure — servers, databases, networks, everything — in a single file?"
>
> **Ravi:** "Like a cheat code for AWS? 😮"
>
> **Rithu:** "Sort of! It's called **CloudFormation**, and it's AWS's way of doing Infrastructure as Code. No more clicking through the console and hoping you didn't miss a step!"

---

## What is CloudFormation?

---

## What Is CloudFormation?

**AWS CloudFormation** is a service that lets you define and provision AWS infrastructure using **code** (YAML or JSON templates).

Instead of manually clicking through the AWS Console to create resources, you write a **template file** describing what you want, and CloudFormation builds it for you.

| Term | Definition |
|------|-----------|
| **Template** | The YAML/JSON code file describing your infrastructure |
| **Stack** | A collection of resources created and managed from one template |
| **Change Set** | A preview of what changes CloudFormation will make before executing |
| **Drift** | When actual resources deviate from what the template defines |

---

## 🤔 Why Do We Need It?

Imagine this scenario:

> **Monday:** You manually create an EC2 instance, RDS database, and VPC through the console.
>
> **Wednesday:** You need the exact same setup for the staging environment.
>
> **Friday:** Production breaks, and you need to recreate everything... from memory. 😱

**CloudFormation prevents this nightmare:**

| Problem | CloudFormation Solution |
|---------|------------------------|
| Manual setup is slow & error-prone | Write it once, deploy it anywhere |
| No version history for infrastructure | Templates live in Git — full history |
| Hard to replicate environments | Same template → same stack → identical setup |
| Disasters require manual rebuilding | Re-run the template to recreate everything |
| No way to preview changes | Change Sets show you what will happen |

---

## 🍳 Real-World Analogy

> **CloudFormation is like a recipe book** 📖

> You write down every ingredient (resources), every step (configurations), and every measurement (properties). Anyone can follow the recipe and get the **exact same dish** every time.

> - **Template** = The recipe
> - **Stack** = The dish being cooked
> - **Parameters** = Adjustable ingredients ("use 2 eggs or 3?")
> - **Outputs** = The final dish you present
> - **Drift** = Someone secretly swapped the butter for margarine 🧈

---

## 📝 Template Structure

A CloudFormation template has several sections. Only **Resources** is required:

```yaml
AWSTemplateFormatVersion: '2010-09-09'   # Template version
Description: 'My first CloudFormation template'  # Description

Parameters:                                 # 🔧 User inputs
  InstanceType:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.medium

Mappings:                                   # 🗺️ Look-up tables
  RegionMap:
    us-east-1:
      AMI: ami-0c55b159cbfafe1f0
    us-west-2:
      AMI: ami-0892d3c7ee96c0bf7

Conditions:                                 # 🔀 Conditional resources
  CreateProductionResources:
    !Equals [!Ref Environment, production]

Resources:                                  # ⭐ THE MAIN EVENT (required)
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      ImageId: !FindInMap [RegionMap, !Ref 'AWS::Region', AMI]
      Tags:
        - Key: Name
          Value: MyWebServer

Outputs:                                    # 📤 What to show after creation
  InstancePublicIP:
    Value: !GetAtt MyEC2Instance.PublicIp
    Description: Public IP of the instance
```

### Section Cheat Sheet

| Section | Required? | Purpose |
|---------|-----------|---------|
| **Resources** | ✅ Yes | Define AWS resources to create |
| **Parameters** | ❌ No | Make templates reusable with user inputs |
| **Outputs** | ❌ No | Return values (e.g., IP address, URL) |
| **Mappings** | ❌ No | Key-value lookups (like a lookup table) |
| **Conditions** | ❌ No | Create resources conditionally |
| **AWSTemplateFormatVersion** | ❌ No | Specifies template version |
| **Description** | ❌ No | Human-readable description |

---

## ⚙️ How CloudFormation Works

```
Step 1: Write Template (YAML/JSON)
              │
              ▼
Step 2: Upload to CloudFormation
              │
              ▼
Step 3: CloudFormation parses & validates
              │
              ▼
Step 4: Creates Change Set (preview)
              │
              ▼
Step 5: Execute Stack (AWS creates resources)
              │
              ▼
Step 6: Monitor via Events in Console
```

### The Stack Lifecycle

```
              CREATE_IN_PROGRESS
                    │
            ┌───────┴───────┐
            ▼               ▼
    CREATE_FAILED    CREATE_COMPLETE ✅
                              │
                              ▼
                    UPDATE_IN_PROGRESS
                              │
                    ┌─────────┴─────────┐
                    ▼                   ▼
            UPDATE_FAILED      UPDATE_COMPLETE ✅
                                      │
                                      ▼
                              DELETE_IN_PROGRESS
                                      │
                              ┌───────┴───────┐
                              ▼               ▼
                      DELETE_FAILED    DELETE_COMPLETE 🗑️
```

---

## 🔄 Key Features

| Feature | What It Does |
|---------|-------------|
| **Change Sets** | Preview changes before applying them — like a dry run |
| **Drift Detection** | Detect when someone manually changed a resource outside CloudFormation |
| **Rollback** | If stack creation fails, AWS auto-rolls back all changes |
| **Stack Policies** | Protect critical resources from being deleted/updated |
| **Termination Protection** | Prevent accidental stack deletion |
| **Import Resources** | Bring existing manually-created resources under CloudFormation management |
| **Nested Stacks** | Break large templates into smaller, reusable sub-stacks |
| **Stack Sets** | Deploy the same stack across multiple accounts & regions |

---

## 🏗️ Architecture Overview

```
┌──────────────────┐
│   Developer       │
│ (writes template) │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐     ┌─────────────────┐
│  CloudFormation  │────▶│  S3 (optional)  │
│    Service        │     │  (template store)│
└────────┬─────────┘     └─────────────────┘
         │
         ├──▶ Creates ──▶ EC2 Instances
         ├── Creates ──▶ RDS Databases
         ├── Creates ──▶ VPC & Subnets
         ├── Creates ──▶ S3 Buckets
         └── Creates ──▶ IAM Roles
              │
              ▼
      ┌──────────────┐
      │  Your Stack   │
      │ (all managed) │
      └──────────────┘
```

---

## 🔄 Alternatives to CloudFormation

| Tool | Cloud Provider | Language | Notes |
|------|---------------|----------|-------|
| **CloudFormation** | AWS only | YAML/JSON | Native, free, deep AWS integration |
| **Terraform** | Multi-cloud | HCL | Most popular IaC tool, uses state files |
| **AWS CDK** | AWS only | Python/TS/Java | Write code, generates CloudFormation templates |
| **Pulumi** | Multi-cloud | Python/TS/Go | Real programming languages for IaC |

> **Rithu:** "CloudFormation is like cooking with a recipe book. Terraform is like cooking with a recipe book that works in ANY kitchen. AWS CDK is like writing your own recipe generator in code! 🧑‍💻"

---

## 🎯 Common Use Cases

| Use Case | How CloudFormation Helps |
|----------|------------------------|
| Multi-environment setup | Same template for dev/staging/prod with Parameters |
| Disaster recovery | Re-deploy entire infrastructure from template |
| CI/CD pipelines | Auto-deploy infrastructure changes via pipeline |
| Quick lab environments | Spin up and tear down test environments |
| Compliance & auditing | Version-controlled infrastructure in Git |
| Team onboarding | New team members can understand infra from code |

---

## ✅ Best Practices

| Practice | Why |
|----------|-----|
| **Use Parameters for reusability** | Same template, different configs per environment |
| **Use Outputs for cross-stack references** | Share values (like VPC IDs) between stacks |
| **Enable termination protection** | Prevent accidental deletion of production stacks |
| **Use stack policies** | Protect critical resources from updates |
| **Store templates in S3 or Git** | Version control and easy access |
| **Use Change Sets** | Always preview before deploying |
| **Keep templates small** | Use nested stacks for large architectures |
| **Tag all resources** | Track costs and ownership |

---

## ❌ Common Mistakes

| Mistake | Impact |
|---------|--------|
| Hardcoding values in templates | Can't reuse across environments |
| Not using Change Sets | Surprises when deploying changes |
| Deleting stacks with critical resources | 💥 Data loss, service outage |
| Not enabling termination protection | Accidental deletion of prod stacks |
| Ignoring drift detection | Resources become inconsistent |
| Putting everything in one giant template | Hard to maintain, slow deployments |
| Not using Parameters | Template only works in one scenario |

---

## 🎤 Interview Questions

### Q1: What is the difference between a CloudFormation Template and a Stack?
**Answer:** A **Template** is the YAML/JSON file that describes what resources to create — it's the blueprint. A **Stack** is the actual collection of AWS resources created from that template — it's the running instance. One template can create many stacks.

### Q2: How does CloudFormation handle failures during stack creation?
**Answer:** By default, CloudFormation performs a **rollback** — it deletes all resources it created if any resource fails to create. You can disable this with the `--on-failure` flag (options: `ROLLBACK`, `DELETE`, `DO_NOTHING`), but rollback is the safe default.

### Q3: What is CloudFormation Drift?
**Answer:** Drift occurs when someone manually modifies a resource outside of CloudFormation (e.g., changing an EC2 instance type via the console). CloudFormation can **detect** drift by comparing the actual resource state against what the template defines, but it won't automatically fix it.

### Q4: What are CloudFormation Change Sets?
**Answer:** Change Sets are previews of what changes CloudFormation will make to a stack before actually executing them. They show you which resources will be created, updated, or deleted — like a "dry run" to avoid surprises.

### Q5: How do you share outputs between CloudFormation stacks?
**Answer:** Use the **Exports** feature in the Outputs section of a parent stack. Child stacks can then reference these exports using `Fn::ImportValue`. This is how you share VPC IDs, subnet IDs, or other values across related stacks.

---

## 📝 Summary

```
┌─────────────────────────────────────────────────────┐
│           CloudFormation Recap                       │
├─────────────────────────────────────────────────────┤
│ Template = Code (YAML/JSON) describing infra        │
│ Stack    = Running collection of AWS resources      │
│ Change Set = Preview of pending changes             │
│ Drift    = Manual changes detected                  │
├─────────────────────────────────────────────────────┤
│ ✅ Version control with Git                          │
│ ✅ Repeatable & consistent deployments              │
│ ✅ Automatic rollback on failure                    │
│ ✅ Drift detection for compliance                   │
├─────────────────────────────────────────────────────┤
│ Alternatives: Terraform, AWS CDK, Pulumi            │
└─────────────────────────────────────────────────────┘
```

> **Ravi:** "So it's like version-controlling my entire AWS setup? I write the code, push it to Git, and CloudFormation builds everything?"
>
> **Rithu:** "Exactly! And if something breaks, it rolls back automatically. No more 'oh no, what did I delete?' moments! 😄 Now let's move on to managing multiple accounts!"

---

**Next:** [22 - AWS Organizations →](../22%20-%20AWS%20Organizations/README.md)
