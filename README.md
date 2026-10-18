# CS471 Lab 05: Amazon S3 (Object Storage) + Static Website

* **Student Name:** Sarah Shahzad
* **Degree Program:** BS Computer Science
* **Batch:** CS2024
* **Semester:** Fall 2026
* **Course Code:** CS471 — Cloud Computing Lab
* **Lab Number & Title:** Lab 05 — Amazon S3 (Object Storage) + Static Website
* **Instructor Name:** Memona Ijaz
* **Date:** 12-October-2026
* **Live S3 Website Endpoint:** `[http://cs471-sarah-portfolio-2026.s3-website.us-east-1.amazonaws.com](http://cs471-sarah-portfolio-2026.s3-website.us-east-1.amazonaws.com)`

---

## 1. Architecture Overview

In accordance with Section 2 of the CS471 Lab 05 manual, this project hosts a static multi-page portfolio directly from an Amazon S3 bucket without provisioning a virtual server.

~~~text
Website Files (HTML / CSS / Images)
        │
        ▼
Amazon S3 Bucket (cs471-sarah-portfolio-2026)
        │
        ▼ (Evaluates Bucket Policy: s3:GetObject)
S3 Static Website Endpoint (HTTP)
        │
        ▼
Client Web Browser
~~~

---

## 2. Repository Structure

~~~text
Lab-05/
├── README.md
├── index.html
├── about.html
├── projects.html
├── style.css
├── images/
│   ├── profile.jpg
│   ├── project-1.jpg
│   ├── project-2.jpg
│   └── project-3.jpg
└── screenshots/
    ├── 01-home-page.png
    ├── 02-about-page.png
    ├── 03-projects-page.png
    ├── 04-s3-object-list.png
    ├── 05-website-hosting-settings.png
    └── 06-bucket-policy.png
~~~

---

## 3. S3 Deployment, Security, and Cost Notes (Tasks 6, 9, & 10)

* **Bucket Name:** `cs471-sarah-portfolio-2026`
* **AWS Region:** `us-east-1`
* **Object Count:** 8 objects (`index.html`, `about.html`, `projects.html`, `style.css`, and 4 files under the `images/` prefix)
* **Object Ownership:** ACLs Disabled (Bucket owner enforced)
* **Versioning Status:** Enabled
* **Block Public Access:** Adjusted at the bucket level as authorized for static website read access; account-wide settings left untouched.

### Applied Bucket Policy (Public Read Only)

~~~json
{
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
~~~

### Security & Scope Justification (Post-Lab Step 3, Item 58)
* **Why Public-Read Access Must Be Narrowly Scoped:** The bucket policy is restricted strictly to `s3:GetObject` so anonymous visitors can view the web pages without being able to upload (`s3:PutObject`), overwrite, or delete (`s3:DeleteObject`) files in the bucket.
* **Why Sensitive Information Must Never Be Uploaded:** Because any file matched by `arn:aws:s3:::cs471-sarah-portfolio-2026/*` is publicly readable over HTTP without authentication, uploading access keys, secret keys, passwords, or private data would expose them to anyone on the internet.

### Storage Classes, Lifecycle Rule Proposal, & Cost Control (Tasks 9 & 10)
* **Website Storage Class Selection:** `S3 Standard` is used for the active website files because it delivers millisecond first-byte latency required for browser rendering. Archive classes (`S3 Glacier Flexible Retrieval` and `S3 Glacier Deep Archive`) are unsuitable because objects in archival tiers cannot be served immediately over HTTP and require minutes to hours to restore.
* **Proposed Lifecycle Rule for Old Non-Website Files:**
  * **Rule Name:** `Archive-Old-Logs`
  * **Filter Prefix:** `logs/`
  * **Schedule:** Transition objects to `S3 Standard-IA` after 30 days, transition to `S3 Glacier Flexible Retrieval` after 90 days, and permanently expire/delete after 365 days.
  * **Purpose:** Reduces monthly storage expenses for historical logs that do not require real-time access while retaining them for a set retention period.
* **Two Cost-Control Actions Taken:**
  1. Checked regional storage pricing and deployed in `us-east-1`.
  2. Removed redundant noncurrent object versions generated during file update testing so they do not accumulate ongoing per-GB storage charges.

---

## 4. Troubleshooting Log (Post-Lab Step 3, Item 57)

* **Problem Encountered:** After uploading the website files, the profile picture on `index.html` failed to load on the live S3 website endpoint (returning an HTTP `404 Not Found` error).
* **Diagnosis:** Checked the uploaded object keys in the Amazon S3 Console (`Objects` tab) and noticed the file key was named `images/profile.jpg.jpg` instead of `images/profile.jpg` because Windows File Explorer had hidden the known `.jpg` extension when renaming the file locally.
* **Fix Applied:** Enabled file name extensions in Windows File Explorer, renamed the local file to `profile.jpg`, deleted `images/profile.jpg.jpg` from the S3 `images/` prefix, uploaded the corrected `profile.jpg` file, and verified the image rendered in an incognito browser window.

---

## 5. EC2 + Apache (Lab 04) vs. S3 Static Website (Lab 05) Comparison

Per Section 5 (Step 4) of the CS471 Lab 05 manual:

| Dimension | EC2 + Apache (Lab 04) | S3 Static Website (Lab 05) |
| :--- | :--- | :--- |
| **What you manage** | OS, Apache web server (`httpd`), OS patches, instance and server configuration | Bucket, uploaded objects, static website hosting, and access settings |
| **Content** | Static files and dynamic server-side applications (with suitable runtime configuration) | Static HTML, CSS, JavaScript, and media assets only; no server-side execution |
| **Operations** | Manage instance capacity, server uptime, and web-server daemon operation | Serverless; no EC2 virtual machine or web server to administer |
| **Security** | Security Groups, OS/server configuration, SSH keys, and web filesystem permissions | Bucket policy, Block Public Access safeguards, IAM identity policies, and object access |
| **Cost factors** | Instance runtime hours, EBS storage volumes, data transfer, and public IPv4 resources | Object storage (GB/month), request counts, data transfer, versions, and enabled features |
| **HTTPS / custom domain** | Can be configured directly on the Apache server or through additional AWS services | S3 website endpoint is HTTP-only; production HTTPS commonly uses Amazon CloudFront and a domain |

---

## 6. Post-Lab Questions & Answers (Items 60–67)

**60. Differentiate a bucket, object, object key, and prefix.**
* **Bucket:** A top-level container in Amazon S3 used to store objects under a globally unique name.
* **Object:** A file stored in S3 along with its associated metadata.
* **Object key:** The unique full name/identifier of an object inside a bucket (for example, `about.html` or `images/profile.jpg`).
* **Prefix:** The shared beginning string of object keys (such as `images/`) that the S3 console displays like a folder, even though S3 uses a flat storage model rather than a traditional hierarchical filesystem.

**61. Why does an S3 static website need object-read access for visitors?**
Public website visitors access the site through a standard web browser without signing in with AWS IAM credentials. Granting public object-read access (`s3:GetObject`) allows the S3 website endpoint to return the HTML, CSS, and image objects to unauthenticated browsers.

**62. How does an IAM identity policy differ from a bucket policy?**
An IAM identity policy is attached to an authenticated identity (IAM user, group, or role) to define what actions that identity is allowed to perform. A bucket policy is a resource-based policy attached directly to an S3 bucket to control who (including anonymous principals via `"*"`) can access the bucket and its objects.

**63. What does Block Public Access protect against?**
Block Public Access provides account-level and bucket-level safeguards that override bucket policies and ACLs to prevent accidental public exposure of objects in S3 buckets.

**64. Why can S3 static website hosting not replace EC2 for every application?**
S3 static website hosting only delivers static files (HTML, CSS, client-side JavaScript, and images) and cannot execute server-side code (such as Python, PHP, Java, or Node.js backends) or maintain server-side database connections.

**65. How does versioning help recovery, and what cost consideration does it introduce?**
Versioning preserves previous versions of an object whenever it is overwritten or deleted, allowing you to restore an earlier version if a bad change is uploaded. However, each stored version takes up storage space in the bucket and incurs standard S3 storage charges.

**66. Which approach would you choose for a simple static portfolio, and why?**
S3 static website hosting is the better choice for a simple static portfolio because it requires zero operating system or Apache server administration, scales automatically, and costs significantly less than running an EC2 instance continuously.

**67. Name two steps you took to improve security and control cost.**
1. **Security:** Scoped the bucket policy strictly to `s3:GetObject` (read-only) and verified that only non-sensitive portfolio demonstration files were uploaded.
2. **Cost Control:** Checked regional pricing before bucket creation and cleaned up unneeded object versions after testing.

---

## 7. Individual Reflection

Completing Lab 05 demonstrated the practical difference between infrastructure-based web hosting on Amazon EC2 (Lab 04) and serverless static hosting on Amazon S3. Migrating my three-page portfolio to S3 showed how object storage uses a flat namespace with keys and prefixes rather than operating system directories, and how bucket policies interact with Block Public Access to serve content safely over HTTP. Diagnosing the double file extension issue (`profile.jpg.jpg`) also reinforced the importance of exact, case-sensitive object keys when linking static assets in cloud storage.
