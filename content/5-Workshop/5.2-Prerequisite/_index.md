---
title: "Prerequisite — IAM & CLI"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

This chapter prepares the AWS environment for the services deployed throughout the workshop. Resources are created in a single Region for consistency, and an **IAM user** is used instead of the root account to follow AWS security best practices. **AWS CLI** is also configured to support resource management and deployment from the command line.

#### Region

All workshop resources are deployed in **Asia Pacific (Singapore)** (`ap-southeast-1`). Using one Region consistently helps services connect reliably and avoids errors caused by cross-Region differences.

#### Create IAM users

For security, the workshop uses an IAM user rather than the root account during deployment.

1. Open **IAM → Users → Create user**.
2. Create the following users:
   - **`dacswebsk-dev`**: primary IAM user for managing and deploying resources via AWS CLI or SDK (do **not** enable **AWS Management Console access**).
   - **`dacswebsk-ses-smtp`** *(optional)*: used when configuring SMTP authentication for Amazon SES.
3. Attach the following **AWS managed policies** directly to **`dacswebsk-dev`**:

```
AmazonAPIGatewayAdministrator
AmazonEC2FullAccess
AmazonRDSFullAccess
AmazonS3FullAccess
AmazonSESFullAccess
AmazonVPCFullAccess
AWSLambda_FullAccess
CloudWatchFullAccessV2
SecretsManagerReadWrite
```

![IAM users list](/images/5-Workshop/5.2-Prerequisite/01-iam-users-list.png)

![Permissions for dacswebsk-dev](/images/5-Workshop/5.2-Prerequisite/02-iam-user-permissions-dacswebsk-dev.png)

#### Configure AWS CLI

After creating the IAM user, configure AWS CLI with the **`dacswebsk-dev`** access key.

```powershell
aws configure
```

Enter the following values:

```
Default region name: ap-southeast-1
Default output format: json
```

Then verify the active account:

```powershell
aws sts get-caller-identity
```

Expected output includes the IAM user ARN, for example:

```
arn:aws:iam::185529490133:user/dacswebsk-dev
```

If the command returns the ARN of the user you created, AWS CLI is configured correctly and ready for the next chapters.

#### Section summary

In this chapter, you prepared the AWS environment for the full workshop: created an IAM user with the required permissions and configured AWS CLI for authentication. All deployment steps in later chapters will use this IAM user. Sensitive values such as access keys and database connection strings will not be stored in source code; they will be managed through **AWS Secrets Manager**.
