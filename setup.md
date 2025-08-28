# CloudLaunch Project

## Task 1: Static Website Hosting and IAM User Permissions

### **Step 1: Create S3 Buckets**

1. **cloudlaunch-site-bucket**
   - Create an S3 bucket with the name `cloudlaunch-site-bucket`.
   - Enable **Static Website Hosting** in the bucket settings.
   - Upload a basic `index.html` (your static website).
   - Set the bucket to be **publicly accessible** (read-only for anonymous users).
   - Optional: Configure **CloudFront** for HTTPS and caching.
   - Website URL: `[Insert S3 Website URL]`
   - CloudFront URL (if applicable): `[Insert CloudFront URL]`

2. **cloudlaunch-private-bucket**
   - Create an S3 bucket with the name `cloudlaunch-private-bucket`.
   - Make sure the bucket is **private** (no public access).
   - Attach **IAM user permissions** for `GetObject` and `PutObject` only (no Delete).

3. **cloudlaunch-visible-only-bucket**
   - Create an S3 bucket with the name `cloudlaunch-visible-only-bucket`.
   - Set it to **private** (no public access).
   - IAM user should be able to **list** the contents but not read or write any objects.

### **Step 2: Create IAM User**

1. **Create IAM User `cloudlaunch-user`**:
   - Go to IAM and create a user named `cloudlaunch-user`.
   - Attach a **custom JSON policy** that restricts access to only the above S3 buckets with the following permissions:
     - `ListBucket` for all three S3 buckets.
     - `GetObject` and `PutObject` for `cloudlaunch-private-bucket`.
     - `GetObject` for `cloudlaunch-site-bucket`.
     - No delete permissions granted.
     - No access to objects in `cloudlaunch-visible-only-bucket`.

**Attached JSON Policy Example:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
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

### **Step 1: Create VPC**

1. **Create a VPC named `cloudlaunch-vpc`**:
   - Go to the VPC Dashboard and create a VPC with the CIDR block `10.0.0.0/16`.

### **Step 2: Create Subnets**

1. **Create the following subnets** in the `cloudlaunch-vpc`:
   - **Public Subnet**: `10.0.1.0/24`
   - **Application Subnet**: `10.0.2.0/24`
   - **Database Subnet**: `10.0.3.0/28`

### **Step 3: Create and Attach Internet Gateway**

1. **Create an Internet Gateway** called `cloudlaunch-igw`.
2. **Attach the Internet Gateway** to the `cloudlaunch-vpc`.

### **Step 4: Configure Route Tables**

1. **Public Subnet Route Table**:
   - Create a route table called `cloudlaunch-public-rt`.
   - Associate it with the **Public Subnet**.
   - Add a route that sends `0.0.0.0/0` (all internet-bound traffic) to the Internet Gateway.

2. **Private Subnet Route Tables**:
   - Create route tables `cloudlaunch-app-rt` and `cloudlaunch-db-rt` for the Application and Database subnets.
   - Do not associate these subnets with the Internet Gateway or NAT Gateway (keep them private).

### **Step 5: Create Security Groups**

1. **Create `cloudlaunch-app-sg`**:
   - This security group is for App servers and allows **HTTP (port 80)** access only from within the VPC (`10.0.0.0/16`).

2. **Create `cloudlaunch-db-sg`**:
   - This security group is for Database servers and allows **MySQL (port 3306)** access from the **Application subnet** (`10.0.2.0/24`).

### **Step 6: IAM User Permissions for VPC**

1. **Grant `cloudlaunch-user` read-only access to VPC resources**:
   - The IAM user should have permissions to **view**:
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
