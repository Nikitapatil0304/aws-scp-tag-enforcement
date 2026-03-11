Enterprise Tag Governance Enforcement using AWS Tag Policies
Project Overview
This project demonstrates how to enforce enterprise tagging standards for cloud resources using AWS governance services.
The goal is to prevent EC2 instances from launching unless mandatory tags are provided. This ensures:
Accurate cost allocation
Proper resource ownership tracking
Better compliance with cloud governance policies
The solution enforces tagging standards using AWS Organizations, Service Control Policies (SCP), and IAM policies.
Technologies & Tools
AWS EC2
AWS Organizations
Service Control Policies (SCP)
IAM Policies
AWS Tag Policies
Problem Statement
In large enterprise cloud environments, EC2 instances were being launched without proper tagging. This created multiple issues:
Difficulty identifying resource owners
Inaccurate cost allocation reports
Compliance audit failures due to missing metadata
To address this, the cloud governance team implemented a rule:
No EC2 instance should be launched without mandatory tags.
Required Tags
The following tags must be provided when launching an EC2 instance.
Tag Key
Description
Name
Resource owner name
emailID
Official email ID
phoneNo
Contact number
Place
Resource location
Example
Copy code

Name = Rahul
emailID = rahul@company.com
phoneNo = 9876543210
Place = Pune
Governance Strategy
Enterprise tagging governance is implemented using the following AWS services.
1. AWS Organizations
AWS Organizations allows centralized management of multiple AWS accounts and enables enforcement of governance policies across accounts.
2. Service Control Policies (SCP)
SCPs enforce mandatory tagging rules by denying EC2 instance creation if required tags are missing.
3. IAM Policies
IAM policies grant users permissions to launch EC2 instances and interact with AWS services.
4. Amazon EC2
EC2 is used to create virtual machines where the tag enforcement policy is validated.
Policy Logic Explanation
A Service Control Policy (SCP) is used to deny EC2 instance creation when required tags are not included in the request.
The policy uses the AWS condition key:
Copy code

aws:RequestTag
If required tags are missing, the following action is denied:
Copy code

ec2:RunInstances
This ensures that users must provide all mandatory tags during instance creation.
Service Control Policy (SCP)
Json
Copy code
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
Tag Policy
The following AWS Tag Policy defines standardized tag keys across the organization.
Json
Copy code
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
Validation
Two validation scenarios were tested.
Test 1 — Launch EC2 Without Tags
Steps
Navigate to EC2 Console
Click Launch Instance
Do not add any tags
Result
Instance launch failed with the error:
Copy code

You are not authorized to perform this operation.
Explicit deny in Service Control Policy.
This confirms that tag enforcement is working correctly.
Test 2 — Launch EC2 With Required Tags
Steps
Navigate to EC2 Console
Click Launch Instance
Add the required tags:
Name
emailID
phoneNo
Place
Result
The EC2 instance launched successfully.
This confirms that instances can only be created when required tags are provided.
Cost Management Benefits
Mandatory tagging improves cost management in several ways.
Cost Allocation
Organizations can track AWS costs by team, project, or department using tags.
Resource Ownership
Tags help quickly identify who owns a resource.
Budget Monitoring
Finance teams can analyze AWS billing reports using tag filters.
Compliance & Governance
Tagging ensures adherence to organizational governance policies.
Project Architecture
Copy code

User
   │
   ▼
IAM Policy (User Permissions)
   │
   ▼
AWS Organizations
   │
   ▼
Service Control Policy (Tag Enforcement)
   │
   ▼
EC2 Instance Launch Request
   │
   ▼
Tag Validation
   │
   ▼
Instance Created (Only if tags exist)

Screenshots
Add the following screenshots in this section.
SCP Attached to Organizational Unit

<img width="1366" height="768" alt="Screenshot (23)" src="https://github.com/user-attachments/assets/77902340-0b31-49ee-b05f-8abf75ded9a7" />

IAM Policy Configuration

<img width="1366" height="721" alt="Screenshot (27)" src="https://github.com/user-attachments/assets/e48a55d8-3a6d-4cc6-95f0-6f3a687d9558" />

Tags Section

<img width="1366" height="768" alt="Screenshot (26)" src="https://github.com/user-attachments/assets/e522b631-2a9d-4c67-837f-758d66203f4f" />


EC2 Launch Failure Without Tags

<img width="1366" height="768" alt="Screenshot (25)" src="https://github.com/user-attachments/assets/e5fdaad3-b123-425f-94a8-d4da514d1574" />


EC2 Launch Success With Tags

<img width="1366" height="717" alt="Screenshot (29)" src="https://github.com/user-attachments/assets/774db9c5-19db-47a4-aeb8-6c209ad6406d" />


Key Learning Outcomes
Implemented AWS governance using Service Control Policies
Enforced mandatory tagging standards
Improved cloud cost visibility
Strengthened enterprise compliance practices
Conclusion
This project demonstrates how organizations can implement enterprise tagging governance using AWS policies.
By enforcing mandatory tags at resource creation time, companies can significantly improve:
Cost tracking
Security governance
Resource ownership visibility
Operational accountability
