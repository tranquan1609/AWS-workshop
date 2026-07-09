---
title: "Workshop overview"
date: 2026-07-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### About the project

This workshop uses **Event Management (DACSWEBSK)** as a sample application to demonstrate how to deploy an **ASP.NET Core MVC** system on AWS. The application supports event management, attendees, schedules, videos, assignments, certificates, and user notifications.

In its original form, the application relied on a locally installed SQL Server, on-server file storage, and Gmail SMTP for email. These choices work well during development, but they limit scalability, availability, and security.

Through this workshop, those components are migrated to AWS managed services such as **Amazon RDS**, **Amazon S3**, **Amazon SES**, and **AWS Lambda**, helping the system run more reliably, scale more easily, and follow cloud architecture best practices.

#### Workshop goals

- Deploy an ASP.NET Core MVC application on AWS using a **multi-tier architecture**.
- Integrate core AWS services — **Amazon EC2**, **Amazon RDS**, **Amazon S3**, **Amazon SES**, **AWS Lambda**, and **Amazon EventBridge** — into one complete system.
- Practice network configuration with **Amazon VPC**, public subnets, private subnets, **NAT Gateway**, and **security groups**.
- Establish monitoring, security, and backup workflows using **Amazon CloudWatch**, **AWS CloudTrail**, **AWS Backup**, and **AWS Secrets Manager**.
- Document the full deployment process with **AWS Management Console** screenshots for the **First Cloud Journey (FCAJ)** report.
- Optimize cost for a workshop environment with right-sized resources such as **EC2 t3.micro** and **Amazon RDS SQL Server Express**.

#### What you will build

In this workshop, we build a complete AWS architecture for the Event Management system step by step. The design covers networking, compute, storage, database, security, and monitoring as follows:

| Layer | Component |
|-------|-----------|
| Network | VPC `dacswebsk-vpc`, public subnets (2 AZs), private subnet, IGW, security groups |
| Data | RDS SQL Server Express `dacswebsk-db` |
| App | EC2 `dacswebsk-app` running ASP.NET Core on port 80 |
| Edge | ALB + Target Group (healthy) + WAF |
| Storage | S3 buckets for events, videos, certificates, uploads |
| Email | SES verified identity |
| Serverless | Lambda jobs + EventBridge cron + API Gateway chatbot |
| Ops | Secrets Manager, CloudWatch, CloudTrail, AWS Backup |

#### System processing flow

After deployment is complete, the application operates as follows:

1. Users access the application through a domain managed by **Amazon Route 53**. HTTP/HTTPS requests are inspected by **AWS WAF** before reaching the **Application Load Balancer (ALB)**.
2. The **ALB** distributes traffic to an **EC2** instance running the ASP.NET Core MVC application.
3. The application handles user requests and stores business data on **Amazon RDS for SQL Server**.
4. Files such as images, videos, and certificates are stored on **Amazon S3** instead of local disk.
5. Background tasks run on **AWS Lambda**, triggered automatically by **Amazon EventBridge** on a schedule or by events.
6. When email or notifications are required, the application uses **Amazon SES** to deliver messages to users.
