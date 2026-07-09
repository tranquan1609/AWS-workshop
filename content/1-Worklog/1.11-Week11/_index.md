---
title: "Week 11 Worklog"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

**Week 11:** 29 June – 3 July 2026

### Week 11 Objectives

- Begin deploying the **Event Management (DACSWEBSK)** project on AWS per the proposed multi-tier architecture.
- Prepare IAM, AWS CLI, and integrate ASP.NET Core code with AWS services.
- Deploy networking (VPC), database (RDS), storage (S3), and email (SES).
- Host the app on EC2 with ALB and WAF; deploy serverless background jobs via Lambda, EventBridge, and API Gateway.

### Tasks for this week

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Study DACSWEBSK AWS architecture and finalize the Proposal.<br>- Create IAM User `dacswebsk-dev`, configure AWS CLI in `ap-southeast-1`, verify with `aws sts get-caller-identity`.<br>- Prepare code: add AWS SDK (S3, SES), `IFileStorageService` with Local/S3 support, `appsettings.json`.<br>- Run the app locally before cloud migration. | 29/06/2026 | 29/06/2026 | [5.1 — Overview](../../5-Workshop/5.1-Workshop-overview/)<br>[5.2 — Prerequisites](../../5-Workshop/5.2-Prerequisite/)<br>[2 — Proposal](../../2-Proposal/) |
| 3 | - Create VPC `dacswebsk-vpc`, public/private subnets, Internet Gateway, Security Groups.<br>- Deploy Amazon RDS SQL Server Express `dacswebsk-db`, configure SG port 1433.<br>- Update connection string, run `dotnet ef database update` to migrate schema to RDS.<br>- Verify user registration and event creation via RDS-connected app. | 30/06/2026 | 30/06/2026 | [5.3 — VPC & Networking](../../5-Workshop/5.3-VPC-Networking/)<br>[5.4 — Amazon RDS](../../5-Workshop/5.4-RDS/) |
| 4 | - Create S3 buckets for event images, videos, certificates, and uploads; enable Block Public Access.<br>- Set `Storage:Provider = S3` in the app; verify event image upload to S3.<br>- Verify SES email identity; configure SES SMTP instead of Gmail.<br>- Test registration/notification emails via SES. | 01/07/2026 | 01/07/2026 | [5.5 — S3 & SES](../../5-Workshop/5.5-S3-SES/) |
| 5 | - Create EC2 instance `dacswebsk-app`, attach IAM Role `DacsWebEc2Role`, publish and run `DACSWEBSK.dll` on port 80.<br>- Configure ALB `dacswebsk-alb` and Target Group health checks.<br>- Attach AWS WAF `dacswebsk-waf` before ALB; verify app access via ALB.<br>- Confirm WAF → ALB → EC2 → RDS flow works stably. | 02/07/2026 | 02/07/2026 | [5.6 — EC2, ALB & WAF](../../5-Workshop/5.6-EC2-ALB-WAF/) |
| 6 | - Deploy 4 Lambda functions: `dacsweb-event-status`, `dacsweb-auto-certificate`, `dacsweb-notification`, `dacsweb-chatbot`.<br>- Create 3 EventBridge cron rules replacing .NET Hosted Services.<br>- Configure API Gateway HTTP API `POST /chat` for chatbot querying RDS.<br>- Test chatbot from web UI and verify scheduled background jobs. | 03/07/2026 | 03/07/2026 | [5.7 — Lambda & EventBridge](../../5-Workshop/5.7-Lambda-EventBridge/) |

### Week 11 Outcomes

- Finalized DACSWEBSK AWS deployment proposal and roadmap covering 15 AWS services.
- Set up IAM User `dacswebsk-dev` and AWS CLI; integrated AWS SDK into ASP.NET Core.
- Deployed VPC, RDS SQL Server Express, and migrated database; app reads/writes RDS instead of local SQL Server.
- Moved file storage to Amazon S3 and email to Amazon SES; verified image upload and notifications.
- Hosted app on EC2 with ALB and WAF; system accessible via `dacswebsk-alb` with Layer 7 protection.
- Deployed Lambda, EventBridge, and API Gateway; offloaded background jobs and chatbot to serverless.
