---
title: "Week 12 Worklog"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

**Week 12:** 6 July – 9 July 2026 *(through Thursday)*

### Week 12 Objectives

- Complete operations and security layer: Secrets Manager, CloudWatch, CloudTrail, AWS Backup.
- Run end-to-end testing of DACSWEBSK business flows on AWS.
- Finalize Workshop documentation, architecture diagram, and FCJ report preparation.

### Tasks for this week

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Store connection string and sensitive config in AWS Secrets Manager (`dacswebskproduction`).<br>- Configure CloudWatch Alarms for EC2 and RDS; enable CloudTrail `dacswebsk-trail`.<br>- Create AWS Backup plan `dacswebsk-backup-plan` for scheduled RDS backups.<br>- Review IAM Roles, Security Groups, and least privilege principles. | 06/07/2026 | 06/07/2026 | [5.8 — Monitoring & Security](../../5-Workshop/5.8-Monitoring-Security/) |
| 3 | - End-to-end testing: registration/login, event admin, image/video upload to S3.<br>- Verify SES emails (confirmation, certificates), Lambda jobs (status update, auto certificate).<br>- Test chatbot via API Gateway (`POST /chat`), app access via ALB.<br>- Document and fix issues (WAF blocking forms, RDS timeout, SES sandbox). | 07/07/2026 | 07/07/2026 | [5.1 — Overview](../../5-Workshop/5.1-Workshop-overview/)<br>[5.7 — Lambda & EventBridge](../../5-Workshop/5.7-Lambda-EventBridge/) |
| 4 | - Complete Workshop documentation (Chapter 5): 9 sections from prerequisites to cleanup.<br>- Update DACSWEBSK architecture diagram in Proposal to match actual deployment.<br>- Reconcile 15 AWS services list with created Console resources. | 08/07/2026 | 08/07/2026 | [5 — Workshop](../../5-Workshop/)<br>[2 — Proposal](../../2-Proposal/) |
| 5 | - Summarize deployment results: demo URLs (ALB, API Gateway), AWS resource list.<br>- Review costs and optimization recommendations (small instances, stop unused resources).<br>- Prepare FCJ internship demo and final report.<br>- Plan resource cleanup or keep demo environment (per 5.9 guide). | 09/07/2026 | 09/07/2026 | [5.9 — Cleanup](../../5-Workshop/5.9-Cleanup/)<br>[2 — Proposal](../../2-Proposal/) |

### Week 12 Outcomes

- Completed operations layer with Secrets Manager, CloudWatch Alarms, CloudTrail, and AWS Backup for security and data recovery.
- Successfully tested main flows: user, admin, S3 upload, SES email, Lambda/EventBridge jobs, and API Gateway chatbot.
- Completed full Workshop documentation with real AWS Console screenshots for FCAJ reporting.
- Updated architecture diagram and Proposal to match deployed system (WAF → ALB → EC2 → RDS; S3/SES; Lambda/EventBridge/API Gateway).
- DACSWEBSK runs stably on AWS `ap-southeast-1`, ready for demo and First Cloud Journey internship completion report.

### Deployment reference

| Component | Endpoint / resource |
| --- | --- |
| Application Load Balancer | `http://dacswebsk-alb-1804460399.ap-southeast-1.elb.amazonaws.com` |
| EC2 (Elastic IP) | `http://18.140.214.160` |
| Chatbot API | `https://tcunvi9s86.execute-api.ap-southeast-1.amazonaws.com/chat` |
| Region | `ap-southeast-1` (Singapore) |
