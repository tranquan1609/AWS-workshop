---
title: "AWS architecture design"
date: 2026-06-25
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# My first AWS architecture design and what I placed in the wrong layer

**Original post:** [AWS Study Group FCJ on Facebook](https://www.facebook.com/share/p/1CfFENFe8k/)

Hello admins and friends in the AWS community. I am a final-year IT student who started learning AWS during my internship to design architecture for a web system.

At first I thought drawing an AWS architecture was simple: if you have EC2, RDS, S3, ALB, NAT Gateway… you just drag icons onto a diagram and connect them. The more I worked, the more I realized: **knowing what a service does does not mean you know where it belongs in the architecture**.

For example, I once wondered whether Amazon S3 and SES should sit inside the VPC, whether the Internet Gateway belongs in a public subnet, whether the ALB sits at Region, VPC, or Availability Zone level, and where to draw an IAM role sensibly.

After some study, I gradually understood how to place things:

- **Internet Gateway** is attached to the VPC but **does not live inside a public subnet**.
- **NAT Gateway** is usually placed in a **public subnet** so resources in private subnets can reach the Internet outbound.
- **EC2** is often placed in a **private subnet** when it does not need direct Internet access.
- **RDS** should be in a **private subnet** and deployed **Multi-AZ** when high availability is required.
- **S3** and **SES** are **Regional** AWS services—they are not directly inside the VPC.
- **IAM** is a **global** service, so it is not tied to a VPC or a specific Region.
- **Application Load Balancer** belongs to the VPC and can span multiple **Availability Zones** through configured subnets.

What I found most interesting is that learning individual services is only the first step. More important is understanding the boundaries of **AWS Cloud**, **Region**, **VPC**, **Availability Zone**, and **Subnet**. Once you understand these layers, placing components on a diagram becomes much more logical.

I also learned that more services does not always mean a better architecture. A small system or student project does not need CloudFront, WAF, Auto Scaling, Multi-AZ, NAT Gateway, and many other services unless there is a real need. Good architecture should solve the right problem, stay understandable, scale when needed, and balance cost.

I am still learning and refining the architecture for my project. There may still be inaccuracies, and I would welcome feedback from experienced AWS practitioners.
