# CS471 Lab 05: Amazon S3 (Object Storage) + Static Website

## Student & Course Details
* **Student Name:** Sarah Shahzad
* **Degree Program:** BS Computer Science
* **Batch:** CS2024
* **Semester:** Fall 2026
* **Course Code:** CS471 — Cloud Computing Lab
* **Lab Title:** Amazon S3 (Object Storage) + Static Website
* **Lab Number:** 05
* **Instructor Name:** Memona Ijaz
* **Date of Completion:** 12-October-2026
* **Live S3 Website Endpoint:** `http://cs471-sarah-portfolio-2026.s3-website.us-east-1.amazonaws.com`

---

## 1. Architecture & Technical Overview

This lab demonstrates zero-server static web hosting on AWS using Amazon S3 object storage. Unlike traditional hosting on virtual machines (EC2 + Apache), the portfolio is served directly from an S3 storage bucket using native static website endpoints and resource-based bucket policies.

```text
[ Web Browser / Client ]
           │
           │  1. HTTP GET Request
           ▼
[ S3 Static Website Endpoint ]
(cs471-sarah-portfolio-2026.s3-website.us-east-1.amazonaws.com)
           │
           │  2. Evaluates Bucket Policy (s3:GetObject Allow)
           ▼
[ Amazon S3 Bucket: cs471-sarah-portfolio-2026 ]
           │
           ├── index.html         (Home Page)
           ├── about.html         (About & Skills)
           ├── projects.html      (Featured Projects)
           ├── style.css          (Modern Stylesheet)
           └── images/            (Object Key Prefix)
               ├── profile.jpg
               ├── project-1.jpg
               ├── project-2.jpg
               └── project-3.jpg




Bucket Name: cs471-sarah-portfolio-2026AWS Region: us-east-1 (US East - N. Virginia)Object Ownership: ACLs Disabled (Recommended)Bucket Versioning: EnabledApplied Bucket Policy:JSON{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowPublicReadForStaticWebsiteLab",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::cs471-sarah-portfolio-2026/*"
    }
  ]
}
Problem Identified: Upon initial upload, the profile image failed to render on the live website (HTTP 404).Diagnosis: Inspected S3 Console objects and found the key was named images/profile.jpg.jpg due to Windows hiding known file extensions during local editing.Resolution Steps:Enabled "File name extensions" in Windows File Explorer.Renamed local file to profile.jpg.Deleted invalid object from S3 and re-uploaded the correct file.Refreshed live endpoint to verify successful rendering.DimensionEC2 + Apache (Lab 04)S3 Static Website (Lab 05)What You ManageOS (Amazon Linux 2023), Apache daemon (httpd), OS patches, security groups, directory permissions.S3 bucket configurations, object assets, static website settings, and bucket access policies.Content SupportedStatic content AND server-side dynamic code (PHP, Python, Node.js, databases).Static HTML, CSS, JavaScript, and media assets only; zero server-side code execution.Operations & ScalingCapacity planning, server uptime monitoring, process restarts, auto-scaling setups.Fully managed serverless infrastructure; automatic scaling and high availability handled by AWS.Security ControlsEC2 Security Groups, Network ACLs, SSH key pairs, Linux OS account permissions.Bucket Policies, IAM Identity Policies, Block Public Access controls, Object ACLs.Cost FactorsContinuous instance hourly rates (e.g., t2.micro), EBS volume storage, Elastic IP fees, egress data transfer.S3 storage per GB/month, HTTP request count (GET/LIST), data transfer out, enabled versioning.HTTPS / Custom DomainSSL/TLS certificates (e.g., Let's Encrypt) installed directly on Apache with custom domain DNS mapping.S3 HTTP endpoints are HTTP-only; production HTTPS and custom domains require an Amazon CloudFront CDN distribution.Q18: Differentiate a bucket, object, object key, and prefix.Bucket: Top-level cloud storage container in Amazon S3 requiring a globally unique name.Object: Fundamental storage entity consisting of file data and key-value metadata.Object Key: Unique sequence of characters forming the full path identifier of an object (e.g., images/profile.jpg).Prefix: String delimiter at the start of a key (e.g., images/) rendered as a folder in the S3 console.Q19: Why does an S3 static website need object-read access for visitors?Anonymous web visitors browse static sites without logging into AWS. The S3 web engine requires explicit public object-read permission (s3:GetObject) via bucket policy to serve static assets over HTTP.Q20: How does an IAM identity policy differ from a bucket policy?An IAM identity policy is attached to users, groups, or roles to define what actions they can perform. A bucket policy is attached directly to an S3 bucket to specify access permissions for that resource.Q21: What does Block Public Access protect against?It serves as a centralized safeguard overriding permissive policies/ACLs to prevent accidental public exposure of private data.Q22: Why can S3 static website hosting not replace EC2 for every application?S3 is strictly a static object store and cannot execute backend server-side scripts (Python, PHP, Node.js), handle server-side sessions, or query relational databases directly.Q23: How does versioning help recovery, and what cost consideration does it introduce?Versioning stores complete iterations of objects when overwritten or deleted, allowing instant rollback. However, retaining multiple versions consumes extra storage space, increasing monthly S3 storage charges.Q24: Which approach would you choose for a simple static portfolio, and why?S3 Static Website Hosting, because it is serverless, highly available, cost-effective, scales automatically with traffic, and eliminates OS server maintenance.Q25: Name two steps you took to improve security and control cost.Security: Restricted bucket policy strictly to s3:GetObject (read-only), preventing unauthorized public writes or deletions.Cost Control: Cleaned up obsolete non-current object versions created during testing to avoid unnecessary per-GB storage fees.💭 Individual ReflectionCompleting Lab 05 provided practical experience with serverless architecture and cloud object storage using Amazon S3. Transitioning my personal portfolio from an EC2-hosted Apache server to an S3 static bucket highlighted the operational advantages of serverless deployments. I gained hands-on understanding of S3 flat key namespaces, object key prefixes, resource-based bucket policies, and static endpoint resolution. Troubleshooting key naming reinforced the importance of exact key paths in cloud storage. Overall, this lab demonstrated why S3 static hosting is the preferred, cost-efficient industry standard for hosting client-side web applications.— Sarah Shahzad (BS Computer Science, Batch CS2024)
