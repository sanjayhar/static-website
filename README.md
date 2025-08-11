#  CI/CD Pipeline for a Static Website

This project demonstrates how to set up a **Continuous Integration / Continuous Deployment (CI/CD)** pipeline using **GitHub Actions** to automatically deploy a static website to **Amazon S3**.

##  Project Overview
The goal of this project is to:
- Version control the static website using **Git & GitHub**
- Set up an **automated deployment** to **AWS S3** whenever changes are pushed to the `main` branch
- Host the website publicly via **S3 Static Website Hosting**
- Ensure secure credential handling using **GitHub Secrets**

---

##  Tech Stack
- **HTML/CSS** – Static site content
- **Git & GitHub** – Version control
- **GitHub Actions** – CI/CD pipeline
- **Amazon S3** – Website hosting
- **AWS CLI** – Manual deployments & debugging
- **YAML** – Workflow configuration

---

##  Features
1. **Static Website** hosted on AWS S3
2. **Automated Deployment** using GitHub Actions
3. **Public Access** via S3 Static Website Hosting
4. **Version Control** with Git
5. **Secure Deployment** using GitHub Secrets
6. **Zero Downtime** updates

---



##  Project Structure

├── index.html # Main static website file

├── README.md # Project documentation

└── .github/

└── workflows/

└── deploy.yml # GitHub Actions workflow for deployment


---

##  Setup Instructions

### 1️ Clone the Repository
```bash
git clone https://github.com/sanjayhar/static-website.git
cd static-website
```

### 2 Create an S3 Bucket

```
aws s3 mb s3://<your-bucket-name> --region ap-south-1
```

Enable Static website hosting in bucket properties

Set Index document to index.html

### 3️ Configure Public Access
Bucket Policy:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::<your-bucket-name>/*"
    }
  ]
}
```

### 4️ Add GitHub Secrets

In GitHub → Settings → Secrets and variables → Actions:

AWS_ACCESS_KEY_ID – Your AWS IAM Access Key

AWS_SECRET_ACCESS_KEY – Your AWS IAM Secret Key

AWS_BUCKET_NAME – Your S3 bucket name

### 5️ Deploy Automatically

Push changes to the main branch

GitHub Actions will run deploy.yml and upload files to S3

```
name: Deploy to S3

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-south-1

      - name: Upload site files to S3
        run: |
          aws s3 sync ./ s3://${{ secrets.AWS_BUCKET_NAME }} \
            --exclude ".git/*" \
            --exclude ".github/*" \
            --delete
```

###  Deployment Verification

Check files in your bucket:

```
aws s3 ls s3://<your-bucket-name> --region ap-south-1
```

Access your site at:
```
http://<your-bucket-name>.s3-website-ap-south-1.amazonaws.com
```
