---
title: "Workshop"
date: 2026-07-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# **Event Management (DACSWEBSK)** on AWS — Event & E-Learning Management Platform

#### Overview

**Event Management (DACSWEBSK)** is an ASP.NET Core 8 MVC application for event organization and online learning. In this workshop, the system was migrated from a local monolithic demo to a production-style architecture on **AWS (ap-southeast-1 / Singapore)**.

The goal is to demonstrate a complete cloud migration path covering compute, database, storage, email, serverless jobs, load balancing, web application security, secrets, backup, and monitoring — with evidence screenshots from a real deployment.

#### Architecture (deployed)

```
Internet
  → AWS WAF (dacswebsk-waf)
  → Application Load Balancer (dacswebsk-alb)
  → Target Group (dacswebsk-tg) → EC2 (dacswebsk-app)
  → Amazon RDS SQL Server (dacswebsk-db)

Chatbot:
  Browser → API Gateway → Lambda (dacsweb-chatbot) → RDS

Background jobs:
  EventBridge → Lambda × 3 (event status / certificate / notification)

Storage & email:
  Amazon S3 (images, videos, certificates, uploads)
  Amazon SES (transactional email)

Ops & security:
  Secrets Manager, CloudWatch Alarms, CloudTrail, AWS Backup
```

#### AWS services used

| # | Service | Resource / role |
|---|---------|-----------------|
| 1 | **IAM** | User `dacswebsk-dev`, EC2 role `DacsWebEc2Role` |
| 2 | **Amazon VPC** | `dacswebsk-vpc`, public/private subnets, security groups |
| 3 | **Amazon RDS** | SQL Server Express `dacswebsk-db` |
| 4 | **Amazon S3** | Buckets for images / videos / certificates / uploads |
| 5 | **Amazon SES** | Verified sender identity |
| 6 | **Amazon EC2** | App host `dacswebsk-app` + Elastic IP |
| 7 | **Elastic Load Balancing** | ALB `dacswebsk-alb` + Target Group |
| 8 | **AWS WAF** | Protection pack `dacswebsk-waf` (18 rules) |
| 9 | **AWS Lambda** | 4 functions (jobs + chatbot) |
| 10 | **Amazon EventBridge** | 3 scheduled cron rules |
| 11 | **Amazon API Gateway** | HTTP API for chatbot |
| 12 | **AWS Secrets Manager** | Secret `dacswebskproduction` |
| 13 | **Amazon CloudWatch** | Alarms for EC2 / RDS |
| 14 | **AWS CloudTrail** | Trail `dacswebsk-trail` |
| 15 | **AWS Backup** | Plan `dacswebsk-backup-plan` for RDS |

#### Content

1. [Workshop overview](5.1-Workshop-overview/)
2. [Prerequisite — IAM & CLI](5.2-Prerequisite/)
3. [VPC & networking](5.3-VPC-Networking/)
4. [Amazon RDS (SQL Server)](5.4-RDS/)
5. [Amazon S3 & SES](5.5-S3-SES/)
6. [EC2, ALB & WAF](5.6-EC2-ALB-WAF/)
7. [Lambda, EventBridge & Chatbot](5.7-Lambda-EventBridge/)
8. [Monitoring & security ops](5.8-Monitoring-Security/)
9. [Cleanup](5.9-Cleanup/)
