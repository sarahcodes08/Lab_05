# ☁️ CS471 Lab 05: Amazon S3 Static Website Hosting

<p align="center">
  <b>Course:</b> CS471 — Cloud Computing Lab | <b>Instructor:</b> Memona Ijaz<br>
  <b>Student:</b> Sarah Shahzad | <b>Batch:</b> CS2024 (BS Computer Science) | <b>Semester:</b> Fall 2026
</p>

---

### 🌐 Live Web Endpoint
> **S3 Website URL:** [http://cs471-sarah-portfolio-2026.s3-website.us-east-1.amazonaws.com](http://cs471-sarah-portfolio-2026.s3-website.us-east-1.amazonaws.com)

---

## 🏗️ Architecture Overview

```text
[ Client Browser ] ───( HTTP GET )───► [ S3 Static Website Endpoint ]
                                                   │
                                                   ▼
                                     [ Evaluates Bucket Policy ]
                                                   │
                                                   ▼
                                   [ S3 Bucket: cs471-sarah-portfolio-2026 ]
                                                   ├── index.html
                                                   ├── about.html
                                                   ├── projects.html
                                                   ├── style.css
                                                   └── images/
                                                       ├── profile.jpg
                                                       ├── project-1.jpg
                                                       ├── project-2.jpg
                                                       └── project-3.jpg
