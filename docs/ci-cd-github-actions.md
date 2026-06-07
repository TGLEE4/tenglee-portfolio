<img width="1672" height="941" alt="Project-10-CI CD-Pipeline" src="https://github.com/user-attachments/assets/3d945530-2eb4-4103-b592-4a49ead51e4d" />

# Project 10: CI/CD with GitHub Actions, Amazon S3, CloudFront, and OIDC

## Overview

This project adds a secure CI/CD deployment pipeline for my live portfolio website:


[https://tenglee.dev](https://tenglee.dev)


Before this project, portfolio updates were deployed manually by copying `index.html` to Amazon S3 and manually creating a CloudFront invalidation.

This project replaces that manual process with an automated GitHub Actions workflow.

Now, when changes are pushed to the `main` branch, GitHub Actions automatically uploads the updated `index.html` file to the S3 website bucket and invalidates the CloudFront cache so the live website updates automatically.

---

## Purpose

The purpose of this project is to make my portfolio deployment process more secure, repeatable, and production-like.

Instead of relying on manual AWS CLI commands, this project uses GitHub Actions to automate deployment after a Git push.

This demonstrates a real cloud engineering workflow:

```text
Code change
↓
Version control
↓
Automated deployment
↓
Cloud infrastructure update
↓
Live website refresh
```

---

## Previous Manual Workflow

Before CI/CD, my deployment process was:

```text
Edit index.html
↓
Commit and push to GitHub
↓
Manually upload index.html to Amazon S3
↓
Manually create a CloudFront invalidation
↓
Refresh https://tenglee.dev
```

This worked, but it required manual AWS CLI steps every time the website changed.

Manual deployment had several weaknesses:

| Issue                                   | Why It Matters                                    |
| --------------------------------------- | ------------------------------------------------- |
| Manual upload required                  | Easy to forget or upload the wrong file           |
| Manual CloudFront invalidation required | The live website could show an old cached version |
| No deployment automation                | The process was slower and less production-like   |
| Human error risk                        | Manual steps are easier to perform incorrectly    |
| No automatic deployment record          | GitHub did not show deployment workflow history   |

---

## New CI/CD Workflow

The new deployment process is:

```text
Edit index.html
↓
Commit changes
↓
Push to GitHub main branch
↓
GitHub Actions workflow starts automatically
↓
GitHub authenticates to AWS using OIDC
↓
GitHub Actions assumes an AWS IAM role
↓
index.html uploads to Amazon S3
↓
CloudFront cache is invalidated
↓
https://tenglee.dev updates automatically
```

This is a more professional deployment workflow because the process is automated, repeatable, and tied to source control.

---

## Architecture

```text
Developer
  ↓
Git commit and push
  ↓
GitHub Repository
  ↓
GitHub Actions Workflow
  ↓
GitHub OIDC Authentication
  ↓
AWS IAM Role
  ↓
Amazon S3 Bucket
  ↓
Amazon CloudFront Invalidation
  ↓
Live Website: https://tenglee.dev
```

---

## Services and Tools Used

| Service / Tool    | Purpose                                                   |
| ----------------- | --------------------------------------------------------- |
| Git               | Tracks local source code changes                          |
| GitHub            | Hosts the portfolio source code repository                |
| GitHub Actions    | Runs the automated deployment workflow                    |
| GitHub OIDC       | Allows GitHub Actions to authenticate to AWS securely     |
| AWS IAM           | Provides the deployment role and permissions              |
| AWS STS           | Issues temporary credentials when GitHub assumes the role |
| Amazon S3         | Stores the static website file `index.html`               |
| Amazon CloudFront | Serves the website globally and caches content            |
| AWS CLI           | Used for setup, verification, and troubleshooting         |

---

## GitHub Actions Workflow

The workflow file is located at:

```text
.github/workflows/deploy-portfolio.yml
```

The workflow runs when changes are pushed to the `main` branch.

The workflow performs these steps:

1. Checks out the repository
2. Configures AWS credentials using GitHub OIDC
3. Uploads `index.html` to the S3 bucket
4. Invalidates CloudFront cache paths `/` and `/index.html`

---

## Workflow File

```yaml
name: Deploy Portfolio to S3 and CloudFront

on:
  push:
    branches:
      - main
    paths:
      - "index.html"
      - ".github/workflows/deploy-portfolio.yml"

permissions:
  id-token: write
  contents: read

jobs:
  deploy:
    name: Deploy tenglee.dev Portfolio
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v5

      - name: Configure AWS credentials with OIDC
        uses: aws-actions/configure-aws-credentials@v6.1.0
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_TO_ASSUME }}
          aws-region: ${{ vars.AWS_REGION }}

      - name: Upload index.html to S3
        run: |
          aws s3 cp index.html s3://${{ vars.S3_BUCKET }}/index.html \
            --content-type "text/html" \
            --cache-control "no-cache"

      - name: Invalidate CloudFront cache
        run: |
          aws cloudfront create-invalidation \
            --distribution-id ${{ vars.CLOUDFRONT_DISTRIBUTION_ID }} \
            --paths "/index.html" "/"
```

---

## Security Design

This project uses GitHub Actions OIDC instead of storing long-term AWS access keys in GitHub.

Security choices included:

* No long-term AWS access keys stored in GitHub
* GitHub Actions assumes an AWS IAM role using OIDC
* IAM trust policy restricts access to the `TGLEE4/tenglee-portfolio` repository
* IAM trust policy restricts deployments to the `main` branch
* IAM permission policy is scoped only to the `tenglee.dev` S3 bucket
* CloudFront permission is scoped only to the portfolio CloudFront distribution
* Deployment variables are stored in GitHub repository variables
* The AWS role ARN is stored as a GitHub secret

---

## IAM Policy Design

This project uses two important IAM policy concepts:

| Policy Type       | Purpose                                                  |
| ----------------- | -------------------------------------------------------- |
| Permission policy | Defines what the GitHub Actions role can do in AWS       |
| Trust policy      | Defines who is allowed to assume the GitHub Actions role |

Simple explanation:

```text
Trust policy = Who can use the role?
Permission policy = What can the role do?
```

For this project:

```text
Trust policy:
Only GitHub repo TGLEE4/tenglee-portfolio on branch main can use the role.

Permission policy:
The role can upload to the tenglee.dev S3 bucket and invalidate the portfolio CloudFront distribution.
```

---

## Permission Policy Scope

The deployment role is intentionally limited.

It can only perform the required deployment actions:

| Permission Area                 | Purpose                                   |
| ------------------------------- | ----------------------------------------- |
| `s3:PutObject`                  | Upload the updated `index.html` file      |
| `s3:DeleteObject`               | Remove outdated files if needed           |
| `s3:ListBucket`                 | List the website bucket during deployment |
| `cloudfront:CreateInvalidation` | Clear the cached version of the website   |

The role does not have full administrator access.

This follows the principle of least privilege.

---

## Repository Secrets and Variables

The workflow uses GitHub Secrets and GitHub Variables.

| Name                         | Type     | Purpose                                             |
| ---------------------------- | -------- | --------------------------------------------------- |
| `AWS_ROLE_TO_ASSUME`         | Secret   | Stores the IAM role ARN that GitHub Actions assumes |
| `AWS_REGION`                 | Variable | Stores the AWS region used by the workflow          |
| `S3_BUCKET`                  | Variable | Stores the target S3 bucket name                    |
| `CLOUDFRONT_DISTRIBUTION_ID` | Variable | Stores the CloudFront distribution ID               |

The IAM role ARN is stored as a secret because it contains AWS account information.

The S3 bucket name, region, and CloudFront distribution ID are stored as variables because they are not secrets.

---

## Deployment Commands

After editing and saving `index.html`, use:

```bash
cd ~/resil-roadmap/tenglee-portfolio

git status

git add index.html
git commit -m "Update portfolio website"
git push origin main

gh run watch
```

Manual S3 uploads and manual CloudFront invalidations are no longer required for normal portfolio updates.

---

## Verification Commands

After pushing, check recent GitHub Actions runs:

```bash
gh run list --limit 5
```

Watch the deployment:

```bash
gh run watch
```

Check the live website response:

```bash
curl -I https://tenglee.dev
```

Expected successful website response:

```text
HTTP/2 200
```

---

## Result

The portfolio website now deploys automatically when changes are pushed to the `main` branch.

Old workflow:

```text
Edit file → manually upload to S3 → manually invalidate CloudFront
```

New workflow:

```text
Edit file → git push → GitHub Actions deploys to S3 → CloudFront cache invalidates automatically
```

This project demonstrates a secure and repeatable CI/CD deployment process similar to what cloud engineers use in production environments.

---

## Skills Demonstrated

* GitHub Actions CI/CD workflow creation
* AWS S3 static website deployment
* Amazon CloudFront cache invalidation
* IAM role design
* IAM permission policies
* IAM trust policies
* GitHub OIDC authentication
* Least-privilege access control
* Secure deployment automation
* Git-based deployment workflow
* Production-style release process
* Secure cloud deployment without long-term access keys

---

## Employer-Focused Summary

Built a secure CI/CD pipeline using GitHub Actions and AWS OIDC to automatically deploy portfolio updates to Amazon S3 and invalidate Amazon CloudFront after each push to the `main` branch.

Designed a least-privilege IAM role scoped to a specific GitHub repository, branch, S3 bucket, and CloudFront distribution, avoiding long-term AWS access keys.

This project demonstrates practical experience with cloud deployment automation, AWS IAM security, GitHub Actions, S3 static website hosting, CloudFront cache management, and production-style CI/CD workflows.
