# AWS EC2 Flask Application for S3 Bucket Content Listing

This project demonstrates setting up a Python-based Flask application on an AWS EC2 instance to interact with an S3 bucket. The application provides endpoints to list the contents of an S3 bucket and its directories.

## Steps to Set Up the Application

### 1. Launch an EC2 Instance
- Create an EC2 instance with a suitable AMI (e.g., Ubuntu 20.04).
- Ensure it has an appropriate security group with necessary inbound and outbound rules.

### 2. Attach an IAM Role to EC2
- Create an IAM Role with the *AmazonS3FullAccess* policy.
- Attach this role to the EC2 instance.

### 3. Configure AWS CLI
- Log in to the EC2 instance via SSH.
- Run the following command to configure AWS CLI:
  ```bash
  aws configure
