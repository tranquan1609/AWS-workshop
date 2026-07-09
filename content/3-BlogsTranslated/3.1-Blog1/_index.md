---
title: "IAM User & IAM Policy"
date: 2026-06-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# IAM User & IAM Policy — Two concepts every AWS learner should master

**Original post:** [AWS Study Group FCJ on Facebook](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206918726739754/)

When I first created my AWS account, I only used the root account because it was the first account AWS provided. It seemed convenient at first, but after learning more, I realized this is one of the most common mistakes for cloud beginners.

The root account has full access to the entire AWS account. If the password or access keys are exposed, all resources are at risk of being deleted or misused.

To reduce this risk, AWS recommends using the root account only for special tasks and handling day-to-day work through an **IAM user** combined with **IAM policies**.

#### Why not use the root account daily?

The root account has full administrative access to every AWS service and resource. It is not limited by any IAM policy, so it can create, modify, or delete anything.

Because access is unlimited, the root account carries significant risk. If credentials leak, an attacker could delete EC2, RDS, or S3 resources, or create new resources, causing data loss and unexpected charges.

AWS recommends not using the root account for daily work. Instead, create IAM users with appropriate permissions. The root account should only be used for tasks such as:

- Changing payment methods.
- Managing billing information.
- Closing the AWS account.
- Performing actions only the root account can perform.

After initial setup, I almost never use the root account and switched to IAM users. This is also an important **AWS security best practice**.

#### IAM user — One account per person

Instead of sharing the root account, I create a separate IAM user for each person.

Each IAM user has:

- Their own username and password.
- Their own MFA for stronger security.
- Their own access keys when using AWS CLI or SDK.

Everyone uses their own account without sharing login details. AWS can also identify who performed each action through **CloudTrail**.

Management becomes easier: create an IAM user for new staff; disable or delete the account when someone leaves.

#### IAM policy — What users are allowed to do

If an IAM user is like an employee, an IAM policy is their job assignment.

For example, an intern who only needs to manage EC2 might be allowed to:

- List instances.
- Start or stop instances.
- Reboot instances.

Permissions such as deleting EC2, deleting Amazon RDS, changing IAM users or policies, or accessing billing would not be granted.

IAM policies ensure each person has only the permissions needed for their work, limiting security risks.

#### Principle of least privilege — Grant only what is needed

AWS recommends the **principle of least privilege**: grant only the permissions required, nothing more.

For example, if an intern only needs to check EC2 and read data from Amazon S3, grant view EC2 and read S3 permissions only. Permissions to delete a VPC, delete Amazon RDS, manage IAM, or access billing should not be granted.

This principle reduces risk from mistakes and strengthens system security.

#### Where can IAM policies be attached?

**IAM user** — The policy applies to one user only. Simple, but harder to manage as the number of users grows.

**IAM group** — Common in enterprises. Attach policies to groups instead of individuals. For example: Developer group manages EC2 and CloudWatch; Tester group is read-only; Finance group accesses billing only. Add new staff to the right group.

**IAM role** — Used when an AWS service needs to act on behalf of a user. For example, if EC2 needs to read from Amazon S3, attach an IAM role to EC2 instead of storing access keys on the server. AWS recommends this approach as safer and easier to manage.

#### Managed policy and inline policy

**Managed policies** can be reused across users, groups, or roles. Benefits: reusable, easier to maintain; updates apply to all attached entities. AWS provides many managed policies such as `ReadOnlyAccess`, `AmazonS3ReadOnlyAccess`, and `AdministratorAccess`.

**Inline policies** attach to only one user, group, or role. If that entity is deleted, the policy is deleted too. Suitable for special cases but less common because they are harder to manage.

#### A practical example

Suppose a team has three members:

- **Intern:** View EC2 and CloudWatch only.
- **Developer:** Deploy applications; manage EC2 and Amazon S3.
- **Administrator:** Full infrastructure management on AWS.

Role-based permissions help everyone do only their job, improving security and manageability.

#### Conclusion

Learning IAM taught me that AWS security is not just about strong passwords—it is about granting the right permissions to the right people.

IAM users separate individuals, IAM policies control exactly what is allowed, and least privilege reduces risk and protects the system. These are foundational concepts when starting with AWS.
