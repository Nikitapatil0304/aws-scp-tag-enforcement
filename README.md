# Enterprise Tag Governance Enforcement using AWS Tag Policies

## Project Overview

This project demonstrates how to enforce enterprise tagging standards for cloud resources using AWS governance services.
The goal is to prevent EC2 instances from launching unless mandatory tags are provided. This ensures accurate cost allocation, resource ownership tracking, and compliance with cloud governance policies.

The solution implements tagging enforcement using AWS governance tools and policies.

---

# Technologies & Tools

* AWS EC2
* AWS Organizations
* IAM Policies
* Service Control Policies (SCP)

---

# Problem Statement

In a large enterprise cloud environment, EC2 instances were being launched without proper tags.
This created several problems:

* Teams could not identify resource owners
* Cost allocation reports were inaccurate
* Compliance audits failed due to missing metadata

To solve this problem, the cloud governance team required that **no EC2 instance should be launched without mandatory tags**.

---

# Required Tags

The following tags must be provided when launching an EC2 instance:

| Tag Key | Description         |
| ------- | ------------------- |
| Name    | Resource owner name |
| emailID | Official email ID   |
| phoneNo | Contact number      |
| Place   | Resource location   |

Example:

Name = Rahul
emailID = [rahul@company.com](mailto:rahul@company.com)
phoneNo = 9876543210
Place = Pune

---

# Governance Strategy

Enterprise tagging governance is implemented using the following AWS services:

1. **AWS Organizations**
   Used to centrally manage multiple AWS accounts and enforce governance policies.

2. **Service Control Policies (SCP)**
   SCPs enforce mandatory tagging rules by denying EC2 instance creation if required tags are missing.

3. **IAM Policies**
   IAM policies provide user permissions to launch EC2 instances and interact with AWS resources.

4. **Amazon EC2**
   EC2 is used to create virtual machines where the tagging policy enforcement is tested.

---

# Policy Logic Explanation

A Service Control Policy is created to deny EC2 instance creation when required tags are not included in the request.

The policy checks the request using the AWS condition key:

aws:RequestTag

If required tags are missing, the following action is denied:

ec2:RunInstances

This ensures that users must provide all mandatory tags during instance creation.

---

# SCP Policy Example

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

# Validation

Two validation scenarios were tested.

## Test 1 – Launch EC2 Without Tags

Steps:

1. Navigate to EC2 console
2. Launch an instance
3. Do not add any tags

Result:

Instance launch failed with error:

"You are not authorized to perform this operation. Explicit deny in Service Control Policy."

This confirms that the tagging policy enforcement is working.

---

## Test 2 – Launch EC2 With Tags

Steps:

1. Navigate to EC2 console
2. Launch an instance
3. Add required tags:

   * Name
   * emailID
   * phoneNo
   * Place

Result:

The EC2 instance launched successfully.

This confirms that instances can only be created when required tags are provided.

---

# Cost Management Benefits

Mandatory tagging provides several cost management benefits:

### Cost Allocation

Organizations can track costs by team, project, or department using tags.

### Resource Ownership

Tags help identify the owner of each resource.

### Budget Monitoring

Finance teams can analyze AWS billing reports using tag filters.

### Compliance & Governance

Tagging ensures compliance with organizational governance policies.

---

# Project Architecture

User
↓
IAM Policy (User Permissions)
↓
AWS Organizations
↓
Service Control Policy Enforcement
↓
EC2 Instance Launch
↓
Tag Validation

---

# Screenshots

The following screenshots demonstrate the implementation:

1. Service Control Policy Creation
2. SCP Attached to Organizational Unit
3. IAM Policy Configuration
4. EC2 Launch Failure Without Tags
5. EC2 Launch Success With Tags

---

# Conclusion

This project successfully implemented enterprise tagging governance using AWS policies.
By enforcing mandatory tags at resource creation time, organizations can improve cost tracking, security governance, and operational visibility across cloud environments.

---
