---
title: "Lambda, EventBridge & Chatbot"
date: 2026-07-01
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

This chapter moves the application's background jobs to a **serverless** architecture using **AWS Lambda** and **Amazon EventBridge**. Instead of running continuously on EC2 as .NET hosted services, tasks are triggered on a schedule and consume resources only when they execute.

The workshop also deploys a **Chatbot API** with **Amazon API Gateway** and **AWS Lambda**, allowing the web UI to interact with Amazon RDS through a serverless path.

#### Move background jobs to serverless

In the local deployment, the application uses three **.NET hosted services** to automatically update event status, generate certificates, and send notifications after events end. On AWS, these tasks become **AWS Lambda functions** written in **.NET 8**, triggered automatically by **Amazon EventBridge**.

| Lambda Function | Role |
|-----------------|------|
| `dacsweb-event-status-update` | Update event status on a schedule |
| `dacsweb-auto-certificate` | Auto-generate certificates for participants |
| `dacsweb-notification` | Send notifications after events end |
| `dacsweb-chatbot` | Handle chatbot requests and query Amazon RDS |

After deployment, review the Lambda function list to confirm all functions were created successfully.

![Lambda functions list](/images/5-Workshop/5.7-Lambda-EventBridge/17-lambda-functions-list.png)

#### Configure Amazon EventBridge

Create **EventBridge rules** to trigger the Lambda functions on a schedule.

| Rule | Description | Status |
|------|-------------|--------|
| `dacsweb-event-status-cron` | Update event status every 5 minutes | Enabled |
| `dacsweb-certificate-cron` | Auto-generate certificates every 15 minutes | Enabled |
| `dacsweb-notification-cron` | Send post-event notifications every 5 minutes | Enabled |

Once configured, Lambda functions run on schedule without needing to stay active on the EC2 server.

![EventBridge rules](/images/5-Workshop/5.7-Lambda-EventBridge/19-eventbridge-scheduled-rules.png)

#### Deploy the chatbot with API Gateway

Beyond background jobs, the workshop deploys a chatbot API using **Amazon API Gateway** and **AWS Lambda**.

Chatbot request flow:

```text
Browser (Chatbot Widget)
        │
        ▼
Amazon API Gateway
        │
        ▼
AWS Lambda (dacsweb-chatbot)
        │
        ▼
Amazon RDS
```

When a user sends a question from the web UI, the request goes to Amazon API Gateway, which invokes the `dacsweb-chatbot` Lambda function. Lambda processes the request, queries Amazon RDS, and returns the result to the application.

Test the Lambda function in the **AWS Lambda console**. If the response is **HTTP 200** with appropriate CORS headers, API Gateway, Lambda, and the web UI are integrated correctly.

![Lambda chatbot test success](/images/5-Workshop/5.7-Lambda-EventBridge/18-lambda-chatbot-test-success.png)

Example Chat API endpoint:

```text
https://tcunvi9s86.execute-api.ap-southeast-1.amazonaws.com/chat
```

#### Section summary

In this chapter, you moved background jobs from **.NET hosted services** to a **serverless** architecture with **AWS Lambda** and **Amazon EventBridge**. This reduces load on EC2, optimizes operating cost, and uses compute only when work is needed. The chatbot is also serverless: **Amazon API Gateway** receives requests and **AWS Lambda** handles business logic before querying Amazon RDS.
