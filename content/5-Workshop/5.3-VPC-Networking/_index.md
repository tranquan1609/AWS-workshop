---
title: "VPC & Networking"
date: 2026-07-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

This chapter builds the network infrastructure for the Event Management system using **Amazon Virtual Private Cloud (Amazon VPC)**. The network architecture follows a multi-tier design: Public Subnets receive traffic from the Internet, while Private Subnets are prepared for internal resources when you scale the system later.

**Security Groups** are configured to control traffic between system components, improving security and following the **least privilege** principle.

#### Create VPC

Create VPC **`dacswebsk-vpc`** with the following settings:

| Item | Value |
|------|-------|
| IPv4 CIDR | `10.0.0.0/16` |
| DNS resolution | Enabled |
| DNS hostnames | Enabled |
| Internet Gateway | `dacswebsk-igw` |

Once complete, the VPC acts as the private network that hosts all resources deployed in this workshop.

![VPC resource map](/images/5-Workshop/5.3-VPC-Networking/03-vpc-dacswebsk-resource-map.png)

#### Subnets

Create the subnets listed below:

| Subnet | AZ | CIDR | Role |
|--------|----|------|------|
| `dacswebsk-subnet-public1-ap-southeast-1a` | `ap-southeast-1a` | `10.0.0.0/20` | EC2 and ALB |
| `dacswebsk-subnet-public2-ap-southeast-1b` | `ap-southeast-1b` | `10.0.16.0/20` | ALB in the second Availability Zone |
| `dacswebsk-subnet-private1-ap-southeast-1a` | `ap-southeast-1a` | `10.0.128.0/20` | Internal resources (optional) |

The two Public Subnets span two Availability Zones to support fault tolerance for the **Application Load Balancer (ALB)**. Public Subnets use route table **`dacswebsk-rtb-public`**, with the default route (`0.0.0.0/0`) pointing to the **Internet Gateway** for Internet access.

![Subnets](/images/5-Workshop/5.3-VPC-Networking/04-vpc-subnets-public-private.png)

#### Security Groups

After the network is in place, create Security Groups to control traffic between system components.

| Security Group | Purpose |
|----------------|---------|
| `dacswebsk-alb-sg` | Allow HTTP traffic (port 80) from the Internet to the ALB |
| `dacswebsk-ec2-sg` | Allow HTTP from the ALB Security Group and SSH/RDP from the administrator IP |
| `dacswebsk-rds-sg` | Allow MSSQL connections (port 1433) from EC2, Lambda, or other VPC resources as needed |
| `dacswebsk-lambda-sg` | Allow Lambda outbound connections when accessing resources inside the VPC |

Separating Security Groups by component makes network traffic easier to control and limits unnecessary access between services.

![Security groups](/images/5-Workshop/5.3-VPC-Networking/05-vpc-security-groups.png)

#### Section summary

In this chapter, you built the core network infrastructure with Amazon VPC: the VPC itself, Public and Private Subnets, Internet Gateway, route table, and Security Groups. The multi-tier design places the ALB across two Availability Zones for higher availability, while Security Groups enforce **least privilege** so the **ALB → EC2 → RDS** traffic path stays protected.
