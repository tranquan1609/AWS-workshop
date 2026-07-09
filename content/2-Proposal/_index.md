---
title: "Proposal"
date: 2026-06-22
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Event Management (DACSWEBSK) on AWS
## An ASP.NET Core deployment solution using AWS managed services

### 1. Executive Summary

**Event Management (DACSWEBSK)** is a web application built on **ASP.NET Core 8 MVC** for event management and online learning. The system provides features such as participant management, schedules, videos, assignments, certificates, email notifications, and a user-support chatbot.

In the current version, the application runs on local infrastructure with SQL Server, file storage on the server, and Gmail SMTP for email. This model works well for early development but has limitations in scalability, availability, and day-to-day operations.

This proposal presents a plan to deploy the system on AWS using managed services such as **Amazon EC2**, **Amazon RDS**, **Amazon S3**, **Amazon SES**, **AWS Lambda**, and **Application Load Balancer**. The solution aims to improve scalability, security, and reliability while preserving the existing application architecture and database.

### 2. Problem Statement

*Current challenges*

In the initial development environment, DACSWEBSK depends on local infrastructure:

- **SQL Server** installed on a private server, hard to back up and scale.
- Images, videos, and certificates stored on the server disk, risking data loss when rebuilding instances.
- Email sent via **Gmail SMTP**, not ideal for production at scale.
- Background jobs run continuously as **.NET hosted services**, consuming server resources even when idle.
- Missing standard cloud protection and observability: WAF, audit trail, automated backup, and centralized secret management.

*Proposed solution*

Deploy DACSWEBSK on AWS using a **multi-tier** model:

- **Amazon RDS for SQL Server Express** replaces local SQL Server.
- **Amazon S3** stores objects for images, videos, certificates, and uploads.
- **Amazon SES** replaces Gmail SMTP.
- **Amazon EC2** hosts the ASP.NET Core application; **ALB** distributes traffic; **AWS WAF** provides Layer 7 protection.
- **AWS Lambda** + **Amazon EventBridge** replace hosted services for background jobs.
- **Amazon API Gateway** + **AWS Lambda** provide a chatbot API that queries RDS.
- **AWS Secrets Manager**, **CloudWatch**, **CloudTrail**, and **AWS Backup** support operations and security.

*Expected benefits*

- Lower infrastructure administration through managed services.
- Better data durability (RDS backup, S3 object storage).
- Serverless background jobs for cost and scale efficiency.
- A real deployment architecture and documentation for the FCJ report and future development.

### 3. Solution Architecture

The deployed system is organized around these main flows:

![DACSWEBSK architecture on AWS](/AWS-workshop/images/2-Proposal/dacswebsk-architecture-diagram.png)

*AWS services used*

| # | Service | Role |
|---|---------|------|
| 1 | **IAM** | User `dacswebsk-dev`, EC2 role `DacsWebEc2Role` |
| 2 | **Amazon VPC** | `dacswebsk-vpc`, public/private subnets, security groups |
| 3 | **Amazon RDS** | SQL Server Express `dacswebsk-db` |
| 4 | **Amazon S3** | Buckets for images / videos / certificates / uploads |
| 5 | **Amazon SES** | Application email delivery |
| 6 | **Amazon EC2** | App host `dacswebsk-app` + Elastic IP |
| 7 | **Elastic Load Balancing (ALB)** | `dacswebsk-alb` + target group |
| 8 | **AWS WAF** | Web ACL `dacswebsk-waf` (managed rules) |
| 9 | **AWS Lambda** | 4 functions (jobs + chatbot) |
| 10 | **Amazon EventBridge** | 3 scheduled cron rules |
| 11 | **Amazon API Gateway** | HTTP API for chatbot |
| 12 | **AWS Secrets Manager** | Secret `dacswebskproduction` |
| 13 | **Amazon CloudWatch** | EC2 / RDS alarms |
| 14 | **AWS CloudTrail** | Trail `dacswebsk-trail` |
| 15 | **AWS Backup** | Plan `dacswebsk-backup-plan` for RDS |

*Component design*

- **Network tier:** VPC with public subnets (2 AZs) for ALB/EC2, private subnet reserved for future scale; Security Groups control ALB → EC2 → RDS traffic.
- **Application tier:** EC2 runs `dotnet DACSWEBSK.dll` on port 80; ALB health checks via target group.
- **Data tier:** RDS stores relational data and ASP.NET Identity; S3 stores media and uploads via `IFileStorageService`.
- **Serverless tier:** Lambda runs cron jobs and chatbot; EventBridge schedules triggers; API Gateway exposes `/chat`.
- **Operations tier:** Secrets Manager stores connection strings; Backup protects RDS; CloudTrail audits activity; CloudWatch raises alerts.

### 4. Technical Implementation

*Implementation phases*

1. **Preparation:** Create IAM user, configure AWS CLI, select Region `ap-southeast-1`.
2. **Network:** VPC, subnets, Internet Gateway, route tables, Security Groups.
3. **Data & storage:** RDS SQL Server, S3 buckets, SES identity.
4. **Application & edge:** EC2, publish ASP.NET Core, ALB, WAF.
5. **Serverless:** Lambda functions, EventBridge rules, API Gateway chatbot.
6. **Operations:** Secrets Manager, Backup, CloudTrail, CloudWatch alarms.
7. **Testing & reporting:** Verify user, admin, upload, email, and chatbot flows; capture Console screenshots.
8. **Cleanup:** Delete resources in dependency order when the workshop ends.

*Technical requirements*

- Application: **ASP.NET Core 8**, **Entity Framework Core**, **ASP.NET Identity**, SQL Server.
- EC2 deployment: Linux or Windows depending on the AMI selected; run the app on port 80.
- S3 integration via `IFileStorageService` (S3 provider); configure SES instead of SMTP.
- Database migration: `dotnet ef database update` on RDS.
- Lambda .NET 8: extract logic from hosted services; chatbot queries RDS via VPC when configured.
- Security: no hardcoded secrets; use Secrets Manager and IAM least privilege.

### 5. Timeline & Milestones

| Phase | Main activities |
|-------|-----------------|
| 1–2  | FCJ onboarding, AWS Console/CLI, EC2 fundamentals |
| 3–4  | DACSWEBSK architecture study, AWS deployment proposal and design |
| 5–6  | Deploy VPC, RDS, S3, SES, EC2, ALB, WAF |
| 7–8  | Lambda, EventBridge, API Gateway chatbot; application integration |
| 9–10 | Secrets Manager, Backup, CloudTrail, CloudWatch; end-to-end testing |
| 11   | Finalize workshop, report, resource cleanup (optional demo retention) |

Step-by-step deployment is documented in [Chapter 5 — Workshop](../5-workshop/).

### 6. Budget Estimation

Cost depends on uptime and instance configuration. For a workshop environment (EC2 `t3.micro`, RDS SQL Server Express `db.t3.micro`, ALB, WAF, scheduled Lambda), estimated cost is roughly **USD 30–80/month** while running continuously.

| Item | Notes |
|------|-------|
| Amazon EC2 `t3.micro` | Application compute host |
| Amazon RDS SQL Server Express | Higher cost than MySQL/PostgreSQL on RDS |
| Application Load Balancer | Fixed fee + LCU |
| AWS WAF | Web ACL and rule charges |
| Amazon S3, Lambda, SES, CloudWatch | Usually low at workshop scale |
| Elastic IP (attached to EC2) | No charge while in use |

**Recommendation:** Use the [AWS Pricing Calculator](https://calculator.aws/) for accurate estimates; stop or delete ALB/RDS/EC2 when not needed; prefer small instances and clean up after the workshop (see [5.9 — Cleanup](../5-workshop/5.9-cleanup/)).

### 7. Risk Assessment

*Risk matrix*

| Risk | Impact | Mitigation |
|------|--------|------------|
| Cost overrun | Medium | Small instances, delete unused resources, monitor Billing |
| WAF blocks file upload | Medium | Set BODY rules to Count or temporarily disassociate WAF during testing |
| RDS connection failure | High | Verify Security Group port 1433, connection string via Secrets Manager |
| SES Sandbox | Low | Verify email; request Production Access for real deployment |
| Data loss | High | AWS Backup for RDS; snapshot before major changes or cleanup |

*Contingency plans*

- Keep RDS snapshots before major changes or cleanup.
- Document Security Group rules and endpoints for quick recovery.
- Fall back to local development if AWS is temporarily unavailable.

### 8. Expected Outcomes

*Technical improvements*

- DACSWEBSK running on AWS with **WAF → ALB → EC2 → RDS** traffic flow.
- File storage on S3; email via SES; background jobs via Lambda/EventBridge.
- Serverless chatbot via API Gateway.
- Observability and security: CloudWatch, CloudTrail, Secrets Manager, Backup.

