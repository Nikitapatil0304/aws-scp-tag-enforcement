# Enterprise Tag Governance Enforcement using AWS Tag Policies

## Project Overview

This project demonstrates how to enforce **enterprise tagging standards** for AWS resources using governance services.

The goal of this project is to **prevent EC2 instances from launching unless mandatory tags are provided**.
This helps organizations maintain proper **resource ownership, cost allocation, and compliance standards**.

The implementation uses:

* AWS Organizations
* Service Control Policies (SCP)
* IAM Policies
* Amazon EC2

---

# Technologies Used

* AWS EC2
* AWS Organizations
* Service Control Policies (SCP)
* IAM Policies
* AWS Tag Policies

---

# Problem Statement

In large cloud environments, many EC2 instances were launched **without proper tagging**.

This caused several problems:

* Resource owners could not be identified
* Cost allocation reports were inaccurate
* Compliance audits failed due to missing metadata

To solve this problem, the cloud governance team implemented a policy:

> **No EC2 instance should be launched without mandatory tags.**

---

# Required Tags

The following tags must be provided during EC2 instance creation.

| Tag Key | Description         |
| ------- | ------------------- |
| Name    | Resource owner name |
| emailID | Official email ID   |
| phoneNo | Contact number      |
| Place   | Resource location   |

### Example

```
Name = CloudAdmin
emailID = cloudadmin@company.com
phoneNo = 9876543210
Place = Pune
```

---

# Governance Strategy

The tagging governance is implemented using the following AWS services.

### AWS Organizations

Used to centrally manage multiple AWS accounts and apply governance policies across the organization.

### Service Control Policies (SCP)

SCPs enforce rules that **deny EC2 instance creation if required tags are missing**.

### IAM Policies

IAM policies allow users to interact with AWS resources and launch EC2 instances.

### Amazon EC2

EC2 is used to test the **tag enforcement mechanism**.

---

# Policy Logic

The Service Control Policy checks whether required tags are present in the request.

If tags are missing, the following action is denied:

```
ec2:RunInstances
```

The policy uses the AWS condition key:

```
aws:RequestTag
```

This ensures that users **must include required tags when launching an instance**.

---

# Service Control Policy (SCP)

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyEC2WithoutTags",
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": "*",
      "Condition": {
        "Null": {
          "aws:RequestTag/Name": "true",
          "aws:RequestTag/emailID": "true",
          "aws:RequestTag/phoneNo": "true",
          "aws:RequestTag/Place": "true"
        }
      }
    }
  ]
}
```

---

# Tag Policy

```json
{
  "tags": {
    "Name": {
      "tag_key": {
        "@@assign": "Name"
      }
    },
    "emailID": {
      "tag_key": {
        "@@assign": "emailID"
      }
    },
    "phoneNo": {
      "tag_key": {
        "@@assign": "phoneNo"
      }
    },
    "Place": {
      "tag_key": {
        "@@assign": "Place"
      }
    }
  }
}
```

---

# Validation

Two test scenarios were performed to validate the tagging enforcement.

---

## Test 1 – Launch EC2 Without Tags

### Steps

1. Navigate to **EC2 Console**
2. Click **Launch Instance**
3. Do not add any tags

### Result

The instance launch fails with the following error:

```
You are not authorized to perform this operation.
Explicit deny in Service Control Policy.
```

---

## Test 2 – Launch EC2 With Required Tags

### Steps

1. Navigate to **EC2 Console**
2. Click **Launch Instance**
3. Add required tags:

* Name
* emailID
* phoneNo
* Place

### Result

The EC2 instance launches successfully.

This confirms that the policy allows instance creation **only when mandatory tags are provided**.

---

# Cost Management Benefits

Mandatory tagging provides multiple benefits for organizations.

### Cost Allocation

Costs can be tracked by teams, projects, or departments using tags.

### Resource Ownership

Tags help identify the owner responsible for each resource.

### Budget Monitoring

Finance teams can analyze billing reports using tag filters.

### Governance and Compliance

Ensures that cloud resources follow organizational standards.

---

# Project Architecture

```
User
   ↓
IAM Policy (User Permissions)
   ↓
AWS Organizations
   ↓
Service Control Policy Enforcement
   ↓
EC2 Instance Launch Request
   ↓
Tag Validation
   ↓
Instance Created (Only if tags exist)
```

---

# Screenshots

## AWS Organization

<img width="1366" height="768" src="https://github.com/user-attachments/assets/c5f4a141-d0c9-40d5-86cb-285f04567830"/>

---

## SCP Policy Creation

<img width="1366" height="768" src="https://github.com/user-attachments/assets/f83a461a-2e88-4457-b32b-7f04921fb83c"/>

---

## Tag Policy

<img width="1366" height="768" src="https://github.com/user-attachments/assets/950bf595-25b5-4568-8eaf-cf8edfc0fc7a"/>

---

## EC2 Launch Without Tags (Failure)

<img width="1366" height="768" src="https://github.com/user-attachments/assets/e6fb07e7-fd43-4223-99bc-8bff0df430a7"/>

---

## Adding Required Tags

<img width="1366" height="447" src="https://github.com/user-attachments/assets/9a6089ab-2f94-48c2-adcb-8971702ddc9c"/>

---

## EC2 Launch With Required Tags

<img width="1366" height="717" src="https://github.com/user-attachments/assets/f0477a86-9bb3-45b8-a3a0-3ddcf08eaa44"/>

---

## EC2 Instance Running With Tags

<img width="1366" height="768" src="https://github.com/user-attachments/assets/78ad14d6-a4a3-409c-b721-8d4aaeb9e017"/>

---

# Key Learning Outcomes

* Implemented **AWS governance using Service Control Policies**
* Enforced **mandatory tagging standards**
* Improved **cloud cost visibility**
* Strengthened **enterprise compliance practices**

---

# Conclusion

This project demonstrates how organizations can enforce **enterprise tagging standards** using AWS governance tools.

By enforcing mandatory tags during resource creation, organizations can improve:

* Cost tracking
* Compliance
* Resource ownership
* Operational governance

---

