# Teng Lee Portfolio Website

## Overview

This repository contains the source code for my live cloud engineering portfolio website:

```text
https://tenglee.dev
```

The portfolio is used to showcase my AWS Cloud Computing Project Roadmap, technical projects, GitHub repositories, and cloud engineering progress.

The site is deployed as a static website using AWS services:

```text
GitHub source code
↓
Local index.html update
↓
Amazon S3 bucket
↓
Amazon CloudFront distribution
↓
Route 53 custom domain
↓
ACM HTTPS certificate
↓
Live website at tenglee.dev
```

This repository stores the source code for the portfolio, while AWS hosts and delivers the live site.

## Purpose

The purpose of this portfolio is to provide a professional public home for my cloud engineering work.

It is designed to show:

| Area                  | Description                                                              |
| --------------------- | ------------------------------------------------------------------------ |
| Cloud projects        | Completed AWS and cloud engineering projects                             |
| GitHub repositories   | Open-source project code and documentation                               |
| Technical progression | Roadmap from AWS basics to containers, Kubernetes, CI/CD, and monitoring |
| Live infrastructure   | A real deployed website using AWS services                               |
| Employer readiness    | Clear proof of hands-on cloud engineering practice                       |

This portfolio supports my goal of becoming a Cloud Infrastructure Engineer.

## Live Site

```text
https://tenglee.dev
```

The live site is served through CloudFront and uses HTTPS.

## Repository

```text
https://github.com/TGLEE4/tenglee-portfolio
```
```markdown
## CI/CD Documentation

This portfolio now includes an automated CI/CD deployment workflow using GitHub Actions, AWS OIDC, Amazon S3, and CloudFront.

Detailed documentation:

```text
docs/ci-cd-github-actions.md

## Architecture

```text
User browser
↓
https://tenglee.dev
↓
Route 53 DNS
↓
CloudFront distribution
↓
S3 bucket origin
↓
index.html
```

## AWS Services Used

| Service                 | Purpose                                                   |
| ----------------------- | --------------------------------------------------------- |
| Amazon S3               | Stores the static website file                            |
| Amazon CloudFront       | Delivers the website globally through a CDN               |
| Amazon Route 53         | Manages DNS for the custom domain                         |
| AWS Certificate Manager | Provides the SSL/TLS certificate for HTTPS                |
| AWS CLI                 | Uploads site updates and creates CloudFront invalidations |

## Current Hosting Flow

The website source code is edited locally in this repository.

After changes are saved, the updated `index.html` file is manually uploaded to the S3 bucket:

```bash
aws s3 cp index.html s3://tenglee.dev/
```

Then CloudFront is invalidated so the newest version is shown to visitors:

```bash
aws cloudfront create-invalidation --distribution-id $(aws cloudfront list-distributions --query "DistributionList.Items[?Aliases.Items[?contains(@, 'tenglee.dev')]].Id" --output text) --paths "/*"
```

This is currently a manual deployment workflow.

There is no GitHub Actions CI/CD deployment connected yet.

## Project Roadmap Displayed

The portfolio displays completed projects from my AWS Cloud Computing Project Roadmap.

Current completed projects include:

| Project   | Name                       | Status            |
| --------- | -------------------------- | ----------------- |
| Project 1 | AWS Account Hardening      | Complete          |
| Project 2 | S3 Static Website          | Complete          |
| Project 3 | EC2 Web Server             | Complete          |
| Project 4 | CloudFront + HTTPS         | Complete          |
| Project 5 | Route 53 Custom Domain     | Complete and live |
| Project 6 | Lambda Serverless Function | Complete          |
| Project 7 | API Gateway + Lambda       | Complete          |
| Project 8 | Docker                     | Complete          |

## Related Project Repositories

| Project                                 | Repository                                                    |
| --------------------------------------- | ------------------------------------------------------------- |
| Project 1 -- AWS Account Hardening      | https://github.com/TGLEE4/resil-project-01-aws-hardening      |
| Project 2 -- S3 Static Website          | https://github.com/TGLEE4/resil-project-02-s3-website         |
| Project 3 -- EC2 Web Server             | https://github.com/TGLEE4/resil-project-03-ec2-webserver      |
| Project 4 -- CloudFront + HTTPS         | https://github.com/TGLEE4/resil-project-04-cloudfront         |
| Project 5 -- Route 53 Custom Domain     | https://github.com/TGLEE4/resil-project-05-route53            |
| Project 6 -- Lambda Serverless Function | https://github.com/TGLEE4/resil-project-06-lambda             |
| Project 7 -- API Gateway + Lambda       | https://github.com/TGLEE4/resil-project-07-api-gateway-lambda |
| Project 8 -- Docker                     | https://github.com/TGLEE4/resil-project-08-docker             |

## Tools & Environment

| Tool                    | Purpose                                          |
| ----------------------- | ------------------------------------------------ |
| WSL2 Ubuntu             | Local Linux development environment              |
| VS Code                 | Code editor opened from WSL using `code .`       |
| Git                     | Version control                                  |
| GitHub                  | Source code hosting                              |
| AWS CLI                 | Deploys updates to S3 and invalidates CloudFront |
| Amazon S3               | Static website file storage                      |
| Amazon CloudFront       | CDN and HTTPS delivery                           |
| Route 53                | DNS routing for `tenglee.dev`                    |
| AWS Certificate Manager | SSL/TLS certificate for HTTPS                    |
| HTML                    | Portfolio page structure                         |
| CSS                     | Portfolio styling                                |

## File Structure

```text
tenglee-portfolio/
├── .gitignore
├── index.html
└── README.md
```

## Important Files

| File         | Purpose                                                      |
| ------------ | ------------------------------------------------------------ |
| `.gitignore` | Defines files Git should ignore                              |
| `index.html` | Main portfolio website file                                  |
| `README.md`  | Documents the portfolio architecture and deployment workflow |

## How the Portfolio Works

The portfolio is a static website.

That means it does not require a backend server, database, or application runtime.

The main file is:

```text
index.html
```

When users visit:

```text
https://tenglee.dev
```

the request follows this path:

```text
Browser
↓
Route 53
↓
CloudFront
↓
S3
↓
index.html
```

CloudFront caches the website so it loads faster for visitors.

Because CloudFront caches the file, updates to S3 may not appear immediately unless a CloudFront invalidation is created.

## Manual Deployment Workflow

Use this workflow any time `index.html` is updated.

### Step 1 -- Go to the portfolio folder

```bash
cd ~/resil-roadmap/tenglee-portfolio
```

### Step 2 -- Open the project in VS Code

```bash
code .
```

### Step 3 -- Edit and save `index.html`

Make the needed changes in VS Code.

Common updates include:

| Update Type          | Example                         |
| -------------------- | ------------------------------- |
| Add new project card | Add Project 9 after completion  |
| Update project count | Change `8+` to `9+`             |
| Update skills        | Add new cloud or DevOps skills  |
| Update links         | Add GitHub repository links     |
| Update text          | Improve descriptions or wording |

### Step 4 -- Check Git status

```bash
git status
```

Expected output should usually show:

```text
modified: index.html
```

This confirms Git sees the local portfolio change.

### Step 5 -- Upload the updated file to S3

```bash
aws s3 cp index.html s3://tenglee.dev/
```

This copies the updated local `index.html` file to the live S3 bucket.

### Step 6 -- Invalidate CloudFront

```bash
aws cloudfront create-invalidation --distribution-id $(aws cloudfront list-distributions --query "DistributionList.Items[?Aliases.Items[?contains(@, 'tenglee.dev')]].Id" --output text) --paths "/*"
```

This clears the cached version of the website so CloudFront pulls the newest file from S3.

### Step 7 -- Check the live website

Open:

```text
https://tenglee.dev
```

If the update does not appear immediately, wait a few minutes and refresh the page.

### Step 8 -- Commit the source code change

```bash
git status
git add index.html
git commit -m "Update portfolio"
git push
```

This saves the source code update to GitHub.

## Full Deployment Command Block

After editing and saving `index.html`, this block can be used to deploy and save the update:

```bash
cd ~/resil-roadmap/tenglee-portfolio

git status

aws s3 cp index.html s3://tenglee.dev/

aws cloudfront create-invalidation --distribution-id $(aws cloudfront list-distributions --query "DistributionList.Items[?Aliases.Items[?contains(@, 'tenglee.dev')]].Id" --output text) --paths "/*"

git status
git add index.html
git commit -m "Update portfolio"
git push
```

## Git Workflow

This repository uses Git for version control.

Typical workflow:

```text
Edit index.html
↓
Check git status
↓
Upload to S3
↓
Invalidate CloudFront
↓
Verify live site
↓
git add
↓
git commit
↓
git push
```

## Deployment Notes

This repository does not currently use automatic deployment.

Pushing to GitHub does not automatically update the live website.

The live site updates only after this command is run:

```bash
aws s3 cp index.html s3://tenglee.dev/
```

and CloudFront is invalidated with:

```bash
aws cloudfront create-invalidation --distribution-id $(aws cloudfront list-distributions --query "DistributionList.Items[?Aliases.Items[?contains(@, 'tenglee.dev')]].Id" --output text) --paths "/*"
```

## Current Manual vs Future Automated Workflow

### Current workflow

```text
Edit locally
↓
Upload to S3 manually
↓
Invalidate CloudFront manually
↓
Commit and push to GitHub
```

### Future CI/CD workflow

A future improvement could use GitHub Actions:

```text
Push to GitHub main branch
↓
GitHub Actions workflow runs
↓
index.html uploads to S3 automatically
↓
CloudFront invalidation runs automatically
↓
Live site updates
```

This would remove the manual AWS CLI deployment steps.

## Security Notes

No secrets should be committed to this repository.

Do not commit:

```text
AWS access keys
.pem files
.env files
Terraform state files
Credentials
Private configuration files
```

AWS access should be managed through the local AWS CLI profile.

## Why This Portfolio Matters

This portfolio is more than a static website.

It demonstrates that I can:

| Skill                        | Demonstrated By                                 |
| ---------------------------- | ----------------------------------------------- |
| Host a static website on AWS | Portfolio is served through S3 and CloudFront   |
| Use a custom domain          | `tenglee.dev` is routed through Route 53        |
| Secure a site with HTTPS     | ACM certificate is used with CloudFront         |
| Use DNS correctly            | Route 53 routes the custom domain               |
| Deploy with AWS CLI          | Updates are pushed to S3 manually               |
| Manage CDN cache             | CloudFront invalidations refresh the live site  |
| Use Git and GitHub           | Source code is version-controlled               |
| Document infrastructure      | README explains the hosting and deployment flow |
| Showcase cloud projects      | Portfolio links to completed roadmap projects   |

## Lessons Learned

Through building and maintaining this portfolio, I learned:

| Concept             | Lesson                                                    |
| ------------------- | --------------------------------------------------------- |
| Static websites     | A portfolio can be hosted without a backend server        |
| S3 hosting          | S3 can store and serve static website files               |
| CloudFront          | A CDN improves delivery and supports HTTPS                |
| Cache invalidation  | Cached files must be invalidated after updates            |
| Route 53            | DNS connects the custom domain to AWS infrastructure      |
| ACM                 | HTTPS certificates are required for secure custom domains |
| GitHub              | Source code should be tracked and publicly reviewable     |
| Deployment workflow | Live changes should match source code changes             |

## Future Improvements

Possible future improvements include:

| Improvement                   | Purpose                                       |
| ----------------------------- | --------------------------------------------- |
| Add GitHub Actions deployment | Automatically upload updates to S3 after push |
| Add separate CSS file         | Make styling easier to maintain               |
| Add project screenshots       | Improve visual presentation                   |
| Add resume download           | Make the portfolio more employer-friendly     |
| Add monitoring                | Track availability and performance            |
| Add accessibility review      | Improve usability for all visitors            |
| Add contact form              | Allow visitors to reach out directly          |

## Employer-Focused Summary

This repository contains the source code for my live cloud engineering portfolio at:

```text
https://tenglee.dev
```

The site is hosted using AWS S3, delivered through CloudFront, routed with Route 53, and secured with an ACM HTTPS certificate.

The portfolio showcases my AWS Cloud Computing Project Roadmap and links to completed hands-on projects covering AWS security, S3, EC2, CloudFront, Route 53, Lambda, API Gateway, and Docker.

This project demonstrates practical experience with static website hosting, DNS, HTTPS, CDN caching, AWS CLI deployments, Git version control, and professional technical documentation.
