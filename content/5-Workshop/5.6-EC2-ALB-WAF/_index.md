---
title: "EC2, ALB & WAF"
date: 2026-07-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

We deploy the **Event Management (DACSWEBSK)** application on **Amazon Elastic Compute Cloud (Amazon EC2)**. After the application is running, **Application Load Balancer (ALB)** distributes incoming traffic and provides a stable entry point for users. Finally, **AWS Web Application Firewall (AWS WAF)** is integrated to protect the application from common Layer 7 threats.

#### Amazon EC2 — Deploy the application

Launch an EC2 instance with the following settings:

| Item | Value |
|------|-------|
| Type | `t3.micro` |
| VPC / Subnet | `dacswebsk-vpc` / Public Subnet `ap-southeast-1a` |
| Security Group | `dacswebsk-ec2-sg` |
| IAM Role | `DacsWebEc2Role` |
| Elastic IP | `18.140.214.160` |

After the instance is created, publish the ASP.NET Core application and start it on port **80**:

```bash
sudo nohup dotnet DACSWEBSK.dll --urls http://0.0.0.0:80 > app.log 2>&1 &
```

If startup succeeds, the EC2 instance status is **Running** and the application is reachable directly via the Elastic IP.

![EC2 Running](/images/5-Workshop/5.6-EC2-ALB-WAF/12-ec2-instance-running.png)

![EC2 instance summary](/images/5-Workshop/5.6-EC2-ALB-WAF/13-ec2-instance-summary-eip.png)

Verify the application by visiting:

```
http://18.140.214.160
```

If the UI loads correctly, EC2 deployment is complete.

#### Application Load Balancer

Next, create an **Application Load Balancer (ALB)** to accept Internet traffic and forward it to the EC2 instance.

Configure the ALB as follows:

- Name: `dacswebsk-alb`
- Scheme: **Internet-facing**
- Availability Zones: `ap-southeast-1a` and `ap-southeast-1b`
- Listener: **HTTP (80)** forwarding to target group `dacswebsk-tg`
- Target: EC2 `dacswebsk-app` on port **80**, status **Healthy**

After configuration, check the load balancer and target group status to confirm the EC2 instance passes health checks.

![ALB Active](/images/5-Workshop/5.6-EC2-ALB-WAF/14-alb-active-listener.png)

![Target Group Healthy](/images/5-Workshop/5.6-EC2-ALB-WAF/15-target-group-healthy.png)

Example ALB DNS name:

```
http://dacswebsk-alb-1804460399.ap-southeast-1.elb.amazonaws.com
```

From this point on, users should access the application through the ALB DNS name rather than the EC2 Elastic IP.

#### AWS WAF

To strengthen application protection, create an **AWS WAF Web ACL** named **`dacswebsk-waf`** (Regional) and associate it with the Application Load Balancer.

The workshop uses **AWS managed rule groups** with **18 rules** to detect and block common attacks such as SQL injection, cross-site scripting (XSS), and abnormal traffic patterns.

![WAF protection pack](/images/5-Workshop/5.6-EC2-ALB-WAF/16-waf-protection-pack.png)

> **Note:** During the lab, the **AWS managed common rule set** may block POST requests with file uploads (for example, creating an event with an image in the admin UI). For easier testing, you can:
>
> 1. Set rule **`SizeRestrictions_BODY`** (and related BODY rules) to **Count**.
> 2. Or temporarily **disassociate** WAF from the ALB while testing file upload.

After testing, restore the original protection settings so the application remains fully protected.

#### Section summary

You have deployed **Event Management (DACSWEBSK)** on **Amazon EC2**, configured **Application Load Balancer** for traffic distribution, and added **AWS WAF** for Layer 7 protection. Together, these components provide a stable, scalable foundation that better meets production requirements.
