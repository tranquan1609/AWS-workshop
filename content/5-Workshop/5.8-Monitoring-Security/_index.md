---
title: "Monitoring, Security & Operations"
date: 2026-07-01
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

This chapter completes the system by adding **monitoring**, **security**, and **operations** services. These services protect sensitive information, track system activity, monitor resource health, and support data recovery when needed.

They are essential steps toward a production-ready deployment on AWS.

#### AWS Secrets Manager

Sensitive values such as the **Amazon RDS connection string**, **Amazon SES** configuration, and access keys should not be stored directly in source code or `appsettings.json`.

In this workshop, production configuration is stored in the secret **`dacswebskproduction`**, which the application retrieves at runtime.

**AWS Secrets Manager** improves security, simplifies credential management, and reduces the risk of leaks when sharing or maintaining source code.

![AWS Secrets Manager](/images/5-Workshop/5.8-Monitoring-Security/20-secrets-manager.png)

#### AWS Backup

To protect system data, create a **backup plan** named **`dacswebsk-backup-plan`** with daily automated backups for the Amazon RDS database.

Associate the plan with resource **`dacswebsk-rds`** so data can be restored after incidents or accidental loss.

![AWS Backup Plan](/images/5-Workshop/5.8-Monitoring-Security/21-backup-plan.png)

#### AWS CloudTrail

Enable **AWS CloudTrail** to record administrative actions and API activity across your AWS account.

Configure the trail with these settings:

- Trail Name: `dacswebsk-trail`
- Trail Logging: **Enabled**
- Multi-region Trail: **Yes**
- Log delivery to Amazon S3

CloudTrail provides an audit trail for your AWS account, supporting investigation, analysis, and traceability when incidents or unexpected changes occur.

![CloudTrail logging](/images/5-Workshop/5.8-Monitoring-Security/22-cloudtrail-logging.png)

#### Amazon CloudWatch

Finally, create **CloudWatch alarms** to monitor system health.

The workshop uses alarms for:

- Remaining Amazon RDS storage capacity.
- Amazon RDS connection count.
- Amazon EC2 health (`StatusCheckFailed`).

When the system is healthy, alarms show **OK**, making it easy for administrators to monitor AWS resource status.

![CloudWatch alarms OK](/images/5-Workshop/5.8-Monitoring-Security/23-cloudwatch-alarms-ok.png)

#### Notes

Some services and settings are simplified to fit the workshop scope.

| Topic | Decision in this workshop |
|-------|---------------------------|
| AWS Shield | Use **AWS Shield Standard** (free) to protect the ALB and Elastic IP against common Layer 3/Layer 4 DDoS attacks. |
| Amazon Route 53 | Not configured — the workshop has no custom domain. Users access the app via the Application Load Balancer DNS name. |
| AWS WAF | Some managed rules may block POST requests with file uploads. During testing, set BODY rules to **Count** or temporarily **disassociate** WAF from the ALB. |

#### Section summary

In this chapter, you added operations and security services for the Event Management system on AWS. **AWS Secrets Manager** secures sensitive configuration, **AWS Backup** protects Amazon RDS with automated backups, **AWS CloudTrail** records account activity, and **Amazon CloudWatch** monitors resources and alerts on issues. Together, these services improve observability, security, and cloud operations.
