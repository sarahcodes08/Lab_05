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
