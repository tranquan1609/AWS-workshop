---
title: "Amazon RDS (SQL Server)"
date: 2026-07-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

Next, we deploy **Amazon Relational Database Service (Amazon RDS)** with **Microsoft SQL Server Express Edition** to replace a locally installed SQL Server database. Amazon RDS significantly reduces database administration effort and provides easier backup, monitoring, and scaling compared to self-managed database servers.

#### Why Amazon RDS for SQL Server?

The **Event Management (DACSWEBSK)** application is built on **ASP.NET Core**, using **Entity Framework Core** with **Microsoft SQL Server** and **ASP.NET Identity**. Choosing **Amazon RDS for SQL Server Express Edition** lets you keep the existing database schema, EF Core migrations, and application code. When deploying to AWS, you only need to update the connection string—no changes to data access logic.

#### Create the database

Create an RDS instance with the following settings:

| Item | Value |
|------|-------|
| Engine | Microsoft SQL Server **Express Edition** |
| DB identifier | `dacswebsk-db` |
| Instance class | `db.t3.micro` |
| Storage | 20 GB (gp2 or gp3) |
| Master username | `admin` |
| Region / AZ | `ap-southeast-1` |

When provisioning completes, the database status changes to **Available**, indicating RDS is ready to accept connections from the application.

![RDS Available](/images/5-Workshop/5.4-RDS/06-rds-databases-available.png)

#### Connectivity

After the database is created, note the connection details for use in the application.

- Endpoint: `dacswebsk-db.xxxxx.ap-southeast-1.rds.amazonaws.com`
- Port: **1433**
- Security Group: `dacswebsk-rds-sg`

These values are used when configuring the connection string for the ASP.NET Core application.

![RDS connectivity / endpoint](/images/5-Workshop/5.4-RDS/07-rds-connectivity-endpoint.png)

#### Configure Security Group

To allow the application to reach the database, configure the RDS Security Group to permit TCP traffic on port **1433** from the following sources:

- VPC CIDR: `10.0.0.0/16`
- Elastic IP of the EC2 instance running the application
- Administrator IP address (temporary use during migration or testing only)
- Rule for the Lambda chatbot demo environment *(restrict or remove in production)*

![RDS SG inbound](/images/5-Workshop/5.4-RDS/08-rds-security-group-inbound-1433.png)

#### Integrate with the application

Once the database is ready, update **DefaultConnection** in the application configuration. In a production deployment, the connection string should be stored and managed through **AWS Secrets Manager** for better security.

Then run Entity Framework Core migrations to create the database schema on Amazon RDS.

```powershell
dotnet ef database update
```

After migration completes, verify the system by registering a new account and creating an event in the admin UI to confirm data is saved successfully to Amazon RDS.

#### Section summary

You have deployed **Amazon RDS for SQL Server Express Edition** as the database for the Event Management system. All relational data—including events, participants, and **ASP.NET Identity** tables—is stored on Amazon RDS. When the database status is **Available** and the Security Group allows connections on port **1433**, the data tier is ready for the application on Amazon EC2.
