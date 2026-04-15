# Static Website Auto-Deploy Pipeline on AWS

A production-ready CI/CD pipeline that automatically deploys a static portfolio website to AWS S3 with CloudFront CDN, triggered on every GitHub push.

---

## Architecture

```
Developer (git push)
       |
       v
  GitHub Repository
       |
       v
GitHub Actions (CI/CD Pipeline)
       |
       v
  AWS S3 Bucket  ──►  CloudFront CDN  ──►  Users (HTTPS)
       |
       v
  CloudWatch (Billing Alerts + Monitoring)
```

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| AWS S3 | Static website hosting |
| AWS CloudFront | CDN + HTTPS delivery |
| AWS IAM | Least-privilege access control |
| AWS CloudWatch | Cost monitoring + alerts |
| GitHub Actions | CI/CD automation pipeline |
| Git | Version control |
| Shell Scripting | Deployment commands (aws s3 sync) |

---

## Features

- Automatic deployment on every `git push` to main branch
- HTTPS enabled via CloudFront CDN
- IAM role with least-privilege S3 access policy
- CloudWatch billing alert for cost monitoring
- Zero manual upload — fully automated pipeline
- Free Tier compatible (no cost on AWS)

---

## Project Structure

```
my-portfolio/
├── index.html                  # Main website file
└── .github/
    └── workflows/
        └── deploy.yml          # CI/CD pipeline configuration
```

---

## CI/CD Pipeline (deploy.yml)

The pipeline is triggered automatically on every push to the `main` branch.

```yaml
name: Deploy to AWS S3

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-south-1

      - name: Deploy to S3
        run: |
          aws s3 sync . s3://dinesh-portfolio-2026 \
            --exclude ".git/*" \
            --exclude ".github/*" \
            --delete
```

---

## Setup Guide

### Prerequisites
- AWS Account (Free Tier)
- GitHub Account
- Git installed on local machine

### Step 1 — Create S3 Bucket
1. Go to AWS Console → S3 → Create bucket
2. Bucket name: `dinesh-portfolio-2026`
3. Region: `ap-south-1` (Mumbai)
4. Uncheck "Block all public access"
5. Enable Static website hosting → Index document: `index.html`
6. Add bucket policy for public read access

### Step 2 — Create IAM User
1. Go to IAM → Users → Create user
2. Username: `github-deployer`
3. Attach policy: `AmazonS3FullAccess`
4. Generate Access Key → choose "Application running outside AWS"
5. Save the Access Key ID and Secret Access Key

### Step 3 — Add GitHub Secrets
Go to your GitHub repo → Settings → Secrets and variables → Actions → New repository secret

| Secret Name | Value |
|-------------|-------|
| `AWS_ACCESS_KEY_ID` | Your IAM Access Key ID |
| `AWS_SECRET_ACCESS_KEY` | Your IAM Secret Access Key |

### Step 4 — Deploy
```bash
git add .
git commit -m "deploy website"
git push origin main
```

Go to GitHub → Actions tab → watch the pipeline run and turn green ✅

---

## How CI/CD Works

1. You write code and run `git push`
2. GitHub detects the push and triggers the Actions workflow
3. GitHub Actions spins up an Ubuntu runner (virtual machine)
4. Runner checks out your code, configures AWS credentials from secrets
5. Runner runs `aws s3 sync` to upload all files to S3
6. S3 serves the updated website instantly
7. CloudFront delivers it globally via HTTPS

**Result:** Every code change goes live automatically in under 60 seconds — no manual upload needed.

---

## AWS Services Used

- **S3** — Stores and serves static website files
- **CloudFront** — CDN with global edge locations + HTTPS (SSL/TLS)
- **IAM** — Secure, least-privilege access management
- **CloudWatch** — Billing alert set at $1 threshold to prevent unexpected charges

---

## Security Practices

- AWS credentials stored as encrypted GitHub Secrets (never hardcoded)
- IAM user follows least-privilege principle
- S3 bucket policy allows only public read (GetObject), not write
- CloudFront enforces HTTPS — HTTP redirected automatically

---

## Author

**Dinesh K**
B.E. Computer Science and Engineering — OASYS Institute of Technology, Tiruchirapalli
AWS Certified | Cloud Engineer | DevOps Enthusiast

- Email: dineshpraveen2211@gmail.com
- LinkedIn: [linkedin.com/in/dinesh-k-7891172a1](https://www.linkedin.com/in/dinesh-k-7891172a1)
- Location: Chennai, India
