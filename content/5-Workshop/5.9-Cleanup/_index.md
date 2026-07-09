---
title: "Resource cleanup"
date: 2026-07-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

After completing the workshop and saving screenshots for your report, clean up unused AWS resources to avoid unexpected charges. Services such as **Application Load Balancer (ALB)**, **AWS WAF**, and **Amazon RDS** continue to incur costs even when the application has no traffic.

This chapter walks through deleting resources in a safe order to reduce dependency errors and leave your AWS environment tidy.

#### Why clean up resources?

Many AWS services are billed by uptime or usage. If resources remain after the workshop ends, your AWS account may keep charging even though the system is no longer in use.

After you finish the lab and report, review and remove resources you no longer need.

#### Recommended cleanup order

To avoid errors from service dependencies, delete resources in this order:

1. **Disassociate** and delete **AWS WAF Web ACL** `dacswebsk-waf`.
2. Delete **Application Load Balancer** `dacswebsk-alb` and **target group** `dacswebsk-tg`.
3. Disable or delete **Amazon EventBridge rules**, then delete **AWS Lambda functions** and **Amazon API Gateway**.
4. Delete **Amazon CloudWatch alarms**.
5. Stop or terminate EC2 instance `dacswebsk-app` and **release** the **Elastic IP**.
6. *(Optional)* Take a final Amazon RDS snapshot before deleting database `dacswebsk-db`.
7. Empty all objects in **Amazon S3 buckets**, then delete the buckets *(you may keep the CloudTrail logs bucket if still needed for review)*.
8. Delete the **AWS Secrets Manager** secret, **AWS Backup** assignment, and **AWS CloudTrail** trail.
9. Finally, delete **security groups**, **subnets**, **Internet Gateway**, and **Amazon VPC**.

#### Keep some resources (optional)

If you plan to keep developing or demoing after the workshop, you do not need to delete everything.

Consider keeping:

- Amazon EC2.
- Amazon RDS.
- Amazon S3.

**Application Load Balancer** and **AWS WAF** often generate ongoing cost, so delete them first if you do not need them.

#### Verify after cleanup

After deletion, use **AWS CLI** to verify a few services:

```powershell
aws ec2 describe-instances --region ap-southeast-1 --filters Name=tag:Name,Values=dacswebsk-app

aws rds describe-db-instances --region ap-southeast-1 --db-instance-identifier dacswebsk-db

aws elbv2 describe-load-balancers --region ap-southeast-1 --names dacswebsk-alb
```

If cleanup succeeded, these commands return **Not Found**, empty results, or no matching resources, depending on the service.

#### Section summary

In this final chapter, you cleaned up the AWS resources created during the workshop. Removing resources after the lab avoids unexpected cost and keeps your AWS environment manageable. It is an important cloud operations practice and one AWS recommends after every trial or workshop.
