# CloudLaunch Project

## Project Overview

**CloudLaunch** is a lightweight cloud platform project designed to demonstrate the fundamentals of AWS, including secure static website hosting, IAM user permissions, and VPC network design.

This project focuses on:

1. Hosting a static website securely using **Amazon S3** and **CloudFront**.
2. Implementing strict access controls using **IAM policies**.
3. Designing a secure **VPC network layout** with public and private subnets.
4. Creating **Security Groups** to control network access between services.

---

## Task 1: Static Website Hosting and IAM User Permissions

### **S3 Buckets**

1. **cloudlaunch-site-bucket**
   - Hosts a basic static website (HTML/CSS/JS).
   - Static website hosting enabled.
   - Publicly accessible (read-only for anonymous users).
   - Website URL: `[https://cloudlaunch-site-bucket-dht.s3.us-east-1.amazonaws.com/index.html]`

2. **cloudlaunch-private-bucket**
   - Private bucket.
   - Accessible only by the IAM user `cloudlaunch-user` for `GetObject` and `PutObject`.
   - No delete permissions granted.

3. **cloudlaunch-visible-only-bucket**
   - Private bucket.
   - IAM user can **list bucket contents**, but cannot read or write objects.

### **IAM User**

- **User:** `cloudlaunch-user`
- Permissions:
  - List all three S3 buckets.
  - Get/Put objects in `cloudlaunch-private-bucket`.
  - Get objects in `cloudlaunch-site-bucket`.
  - Cannot delete objects in any bucket.
  - Cannot access objects in `cloudlaunch-visible-only-bucket`.

**Attached JSON Policy Example:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListAllMyBuckets",
      "Effect": "Allow",
      "Action": "s3:ListAllMyBuckets",
      "Resource": "*"
    },
    {
      "Sid": "GetBucketLocation",
      "Effect": "Allow",
      "Action": "s3:GetBucketLocation",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": ["s3:ListBucket"],
      "Resource": [
        "arn:aws:s3:::cloudlaunch-site-bucket",
        "arn:aws:s3:::cloudlaunch-private-bucket",
        "arn:aws:s3:::cloudlaunch-visible-only-bucket"
      ]
    },
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": ["arn:aws:s3:::cloudlaunch-private-bucket/*"]
    },
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject"],
      "Resource": ["arn:aws:s3:::cloudlaunch-site-bucket/*"]
    }
  ]
}
```

---

## Task 2: VPC Design

### **VPC**

- **Name:** cloudlaunch-vpc
- **CIDR Block:** 10.0.0.0/16

### **Subnets**

| Subnet Name        | CIDR Block  | Type    | Purpose                       |
| ------------------ | ----------- | ------- | ----------------------------- |
| Public Subnet      | 10.0.1.0/24 | Public  | Future public-facing services |
| Application Subnet | 10.0.2.0/24 | Private | App servers                   |
| Database Subnet    | 10.0.3.0/28 | Private | Database servers              |

### **Internet Gateway**

- **Name:** cloudlaunch-igw
- **Attached to:** cloudlaunch-vpc

### **Route Tables**

1. **Public Subnet Route Table (`cloudlaunch-public-rt`):**
   - Associated with **Public Subnet**.
   - Route: `0.0.0.0/0 → cloudlaunch-igw` (Internet access enabled).

2. **Private Subnet Route Tables (`application-rt`, `db-rt`):**
   - Associated with **Application** and **Database** subnets.
   - No routes to the Internet (fully private).

### **Security Groups**

| Security Group     | Purpose                                  | Rules                                    |
| ------------------ | ---------------------------------------- | ---------------------------------------- |
| cloudlaunch-app-sg | App servers (HTTP access within VPC)     | Allow HTTP (port 80) from 10.0.0.0/16    |
| cloudlaunch-db-sg  | Database servers (MySQL access from App) | Allow MySQL (port 3306) from 10.0.2.0/24 |

### **IAM User Permissions for VPC**

- `cloudlaunch-user` has **read-only access** to VPC resources:
  - Subnets
  - Route Tables
  - Security Groups

---

## **Credentials and Access**

- **AWS Account ID / Alias:** `[Insert Account ID / Alias]`
- **IAM User:** `cloudlaunch-user`
- **Temporary Password:** `[Insert Temp Password]` (user must change on first login)

---

## **Summary**

This project demonstrates:

- Secure S3 bucket setup with public/private access.
- Fine-grained IAM policy management.
- Private and public network segmentation using a VPC.
- Security Groups enforcing network-level isolation.
- Optional CloudFront integration for HTTPS static website hosting.

---
