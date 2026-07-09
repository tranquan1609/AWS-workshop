---
title: "AWS learning resources for beginners"
date: 2026-06-15
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Learning AWS is easier when you have the right resources

**Original post:** [AWS Study Group FCJ on Facebook](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206923336739293/)

Here are the sources I recommend for beginners.

If you decide to learn AWS today, the first thing that might overwhelm you is probably not EC2 or VPC—it is the thousands of resources that appear after a single search.

Videos, blogs, courses, official docs... each source teaches differently, and I used to not know where to start. After some time, I realized that choosing the right materials and following a sensible path makes learning AWS much easier.

#### Do not try to learn every AWS service at once

A mistake I made early on was trying to learn too many services at the same time.

One day EC2, the next Lambda, then EKS a few days later. Each service has many concepts and usage patterns, so the more I jumped around, the more confused I became and the less I remembered.

I changed my approach. Instead of learning by service name alone, I learned by foundation level and how services connect.

The path I chose:

- **IAM** — understand users and permissions.
- **Amazon VPC** — understand networking.
- **Amazon EC2** — deploy servers.
- **Amazon S3** — store data.
- **Amazon RDS** — manage databases.

Learning in this order helped me understand each service’s role and how they fit together in a real system. That made learning much easier and more effective.

#### AWS Skill Builder — A starting point for beginners

If I could pick only one resource to start with, I would choose [AWS Skill Builder](https://skillbuilder.aws/).

It is AWS’s own online learning platform with many free courses for beginners. Content is organized by level and is easy to follow even without prior cloud knowledge.

What I like most: learning plans that show what to study first, quizzes to check understanding, and some free hands-on labs to practice after each lesson.

For newcomers, this is an excellent foundation before diving into advanced services.

#### AWS Documentation — The resource I return to most

At first I avoided [AWS Documentation](https://docs.aws.amazon.com/) because I thought it would be hard to read. The more I learned, the more I saw it is the most accurate and up-to-date source. When I need to understand a service or verify a feature, I read the docs first, then practice.

For example, when learning IAM, instead of watching many different videos, I read AWS guides on creating IAM users, writing policies, and applying least privilege. I used the same approach for VPC, EC2, and S3.

#### AWS Well-Architected — Build systems the right way from the start

The [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/) helped me design systems using AWS recommendations instead of only knowing individual services. Principles like security, reliability, and cost optimization shifted my mindset from “it works” to “it is built correctly.”

#### YouTube — Learn from AWS experts

Rather than random videos from many channels, I prioritize official sources such as **AWS Events**, **AWS Online Tech Talks**, and **AWS re:Invent**. The key is to practice along with the video—watching alone is easy to forget.

When a video shows how to create a VPC or deploy EC2, I open the AWS Console and follow step by step. Doing it myself helps me remember far longer than watching and moving on.

#### AWS Free Tier — Learn by doing

Whenever I study a new service, I create real resources on the [AWS Free Tier](https://aws.amazon.com/free/), try them out, then delete them when done. Repeating this helps knowledge stick and avoids unnecessary cost.

#### GitHub — Practical material many people overlook

Once I had basics, I often looked at projects on GitHub such as **Awesome AWS**, **AWS Samples**, and **Terraform Examples** to see how others deploy real systems. Reading code is a great way to see how knowledge applies in projects.

#### Mistakes I made while self-studying AWS

Looking back, I often:

- Read too much and practiced too little, so I forgot quickly.
- Studied too many services at once without depth in any of them.
- Avoided documentation thinking it was too hard, when it is actually the most reliable source.
- Forgot to delete resources after labs, which can cause unexpected charges.

#### Conclusion

Learning AWS does not need to be rushed. What matters is the right path, regular practice, and making good use of free resources.

It is not about how many materials you have—it is about choosing the right ones and practicing consistently. With free AWS and community resources alone, I built a solid foundation, completed labs, and understood how services work together.

If you are just starting, do not try to learn everything at once. Start with core services, practice often, and use free resources before paying for courses.
