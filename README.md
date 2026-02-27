# AWS Project — Secure Static Site (S3 + CloudFront + IAM + Budget Guardrails)

## Project Goal
Deploy a production-grade static website on AWS using S3 as private origin and CloudFront as the public-facing CDN — enforcing HTTPs, least-privilege IAM, and cost guardrails throughout.

This project is part of a AWS Solution Architect Associate (SAA-C03) study plan and demonstrates secure, cost-conscious cloud architecture.

---

## Architecture Overview
```
User (Browser)
     │
     ▼ HTTPS only
┌─────────────────┐
│   CloudFront    │  ← CDN, TLS termination, caching
│  Distribution   │
└────────┬────────┘
         │ Private origin (OAC)
         ▼
┌─────────────────┐
│   S3 Bucket     │  ← Private, encrypted, Block Public Access ON
│  (Static Files) │
└─────────────────┘

IAM: Least-privilege user (no AdministratorAccess)
Billing: AWS Budget alert at $10 threshold
```

---

## Security Controls Applied
| Control                  | Implementation                          |
|--------------------------|-----------------------------------------|
| Root account protection  | MFA enabled, not used for daily work    |
| S3 access                | Block Public Access ON, SSE-S3 encryption |
| CloudFront               | HTTPS enforced (HTTP redirected)        |
| IAM                      | Scoped policy - only S3 + CloudFront actions |
| Cost protection          | AWS Budget alert at $8 (80%) and $10 (100%) |

---

## Project Structure
```
aws-week1-static-site/
├── index.html
├── styles.css
├── error.html
└── README.md
```

---

## How to Deploy (Runbook)

> Prerequisites: AWS account with MFA, IAM user with scoped permissions, Git installed

### Step 1 — Create S3 Bucket
1. Go to S3 -> Create bucket
2. Name: `your-name-week1-static-<unique>`
3. Block all public access: **ON**
4. Default encryption **SSE-S3**
5. Upload `index.html` and `styles.css`

### Step 2 — Create CloudFront Distribution
1. Go to CloudFront -> Create distribution
2. Orgin: select your S3 bucket
3. Orgin access: **Private (OAC recommended)**
4. Viewer protocol policy **Redirect HTTP to HTTPS**
5. Wait ~10 mins for deployment

### Step 3 — Test
- Open the CloudFront domain: `https://dxxxx.cloudfront.net`
- Confirm site loads over HTTPS
- Confirm direct S3 URL returns Access Denied

---

## Troubleshooting

**Site shows old content after an update**
→ Go to CloudFront → Invalidations → Create invalidation → enter `/*`

**Access Denied when opening S3 URL directly**
→ Expected behaviour. The bucket is private. Always use the CloudFront URL.

**CloudFront distribution stuck in "In Progress"**
→ Wait up to 15 minutes. It is propagating to edge locations globally.
