---
title: "Published Blogs"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

During the **First Cloud Journey (FCJ)** internship, our group published blog posts sharing AWS learning experiences in the [AWS Study Group FCJ](https://www.facebook.com/groups/awsstudygroupfcj/) Facebook group. The posts are written from a beginner’s perspective, focusing on concepts that matter when deploying real projects—including the **Event Management (DACSWEBSK)** workshop.

This page provides **context, purpose, and key takeaways** for each post. Full content is on the child blog pages below.

---

### [Blog 1 — IAM User & IAM Policy](3.1-Blog1/)

**Topic:** Two foundational concepts every AWS learner should master.

Many newcomers—including me—use the **root account** for everything because it is the first account AWS provides. The post explains why that is a common mistake: the root has full access, is not limited by IAM policies, and leaked credentials can lead to deleted resources or unexpected charges.

The blog covers AWS security best practices:

- Create a separate **IAM user** per person (own username, MFA, access keys).
- Use **IAM policies** for role-based permissions—for example, an intern may view/start/stop EC2 but not delete RDS or access billing.
- Apply the **principle of least privilege**.
- Compare attaching policies to users, groups, and roles; explain **managed** vs **inline** policies.

**Project link:** In the DACSWEBSK workshop, I created IAM user `dacswebsk-dev` with appropriate managed policies instead of using root [section 5.2 — Prerequisite IAM & CLI](../5-workshop/5.2-prerequisite/).

**Source:** [Facebook post](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206918726739754/)

---

### [Blog 2 — Free resources for learning AWS](3.2-Blog2/)

**Topic:** Learning AWS is easier with the right materials.

Beginners are often overwhelmed by thousands of videos, blogs, and courses after one search. The post describes moving from jumping between services (EC2 → Lambda → EKS…) to a **structured path**: IAM → VPC → EC2 → S3 → RDS, to understand how services fit together in a real system.

The blog summarizes free resources used most:

| Source | Role |
|--------|------|
| **AWS Skill Builder** | Learning plans, quizzes, hands-on labs for beginners |
| **AWS Documentation** | Most accurate source to verify features |
| **AWS Well-Architected** | Design systems correctly (security, reliability, cost) |
| **YouTube** (AWS Events, re:Invent) | Learn from demos—practice in the Console |
| **AWS Free Tier** | Create real resources, delete when done |
| **GitHub** (AWS Samples, Awesome AWS) | Study real deployments |

It also lists **common mistakes**: too much reading and too little practice, studying too many services at once, avoiding documentation, forgetting to delete resources after labs.

**Project link:** Learning path IAM → VPC → EC2 → S3 → RDS.

**Source:** [Facebook post](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206923336739293/)

---

### [Blog 3 — AWS architecture design](3.3-Blog3/)

**Topic:** My first AWS architecture design and what I placed in the wrong layer.

An IT student shares the internship experience of drawing architecture for a web system: at first it seemed enough to drag EC2, RDS, S3, ALB, NAT Gateway icons and connect them—but **knowing what a service does is not the same as knowing which layer it belongs on**.

Questions the author struggled with:

- Should S3 and SES be drawn **inside the VPC**?
- Does the Internet Gateway sit in a **public subnet**?
- Is the ALB at **Region**, **VPC**, or **Availability Zone** level?
- Where should an **IAM role** appear on the diagram?

The post summarizes better placement:

| Component | Architectural layer |
|-----------|---------------------|
| Internet Gateway | Attached to VPC, **not** inside a subnet |
| NAT Gateway | Usually in a **public subnet** |
| EC2 | **Private subnet** when no direct Internet access is needed |
| RDS | **Private subnet**; Multi-AZ when HA is required |
| S3, SES | **Regional** services, outside the VPC |
| IAM | **Global** service |
| ALB | Belongs to VPC, spans multiple **AZs** via subnets |

The post also stresses: good architecture is not “more services is better”—a student project does not need every service (CloudFront, WAF, Auto Scaling, NAT…) without real requirements.

**Project link:** When designing **DACSWEBSK** architecture, I applied Region → VPC → AZ → Subnet layers; the workshop deploys VPC `dacswebsk-vpc`, multi-AZ ALB, and RDS in the VPC—see [Chapter 5 — Workshop](../5-workshop/).

**Source:** [Facebook post](https://www.facebook.com/share/p/1CfFENFe8k/)
