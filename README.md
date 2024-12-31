# AWS EC2 Flask Application for S3 Bucket Content Listing

This project demonstrates setting up a Python-based Flask application on an AWS EC2 instance to interact with an S3 bucket. The application provides endpoints to list the contents of an S3 bucket and its directories.

## Steps to Set Up the Application
  ```bashfrom flask import Flask, jsonify
import boto3

app = Flask(__name__)
s3 = boto3.client('s3')
BUCKET_NAME = 'list-bucket-content-demo'

@app.route('/list-bucket-content/<path:subpath>', methods=['GET'])
@app.route('/list-bucket-content', defaults={'subpath': ''}, methods=['GET'])
def list_bucket_content(subpath):
    prefix = subpath.strip('/') + '/' if subpath else ''
    response = s3.list_objects_v2(Bucket=BUCKET_NAME, Prefix=prefix, Delimiter='/')

    contents = []
    if 'CommonPrefixes' in response:
        contents.extend([cp['Prefix'].rstrip('/') for cp in response['CommonPrefixes']])
    if 'Contents' in response:
        contents.extend([obj['Key'].replace(prefix, '') for obj in response['Contents'] if obj['Key'] != prefix])

    return jsonify({'content': contents})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
 ```
### 1. Launch an EC2 Instance
- Create an EC2 instance with a suitable AMI (e.g., Ubuntu 20.04).
- Ensure it has an appropriate security group with necessary inbound and outbound rules.
 ```bash
install sudo apt install python3-pip -y
 pip install flask boto3
 create s3-app.py file
```

### 2. Attach an IAM Role to EC2
- Create an IAM Role with the *AmazonS3FullAccess* policy.
- Attach this role to the EC2 instance.

### 3. Configure AWS CLI
- Log in to the EC2 instance via SSH.
- Run the following command to configure AWS CLI:
  ```bash
  aws configure
``
  
### 4. Create S3 bucket And give Policy
```bash{
    "Version": "2012-10-17",
    "Id": "Policy1735567338835",
    "Statement": [
        {
            "Sid": "Stmt1735567337153",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::taskwalibucket/*"
        }
    ]
}
```
### Some Activity
```bash
 ec2 launch
 create role with s3fullaccess policy and attach to the ec2
 aws configure
 create bucket and file structure in bucket
 install sudo apt install python3-pip -y
 pip install flask boto3
 create s3-app.py file
 Run the Service >>  python3 app.py
 Add an inbound rule to allow traffic on port 5000
 Test the Service:
curl http://<EC2_PUBLIC_IP>:5000/list-bucket-content
curl http://<EC2_PUBLIC_IP>:5000/list-bucket-content/dir1
Set Up as a Systemd Service
sudo vi /etc/systemd/system/list-bucket.service

Description=S3 Bucket HTTP Service
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu
ExecStart=/usr/bin/python3 /home/ubuntu/app.py
Restart=always

[Install]
WantedBy=multi-user.target
Enable and Start the Service
sudo systemctl daemon-reload
sudo systemctl enable list-bucket.service
sudo systemctl start list-bucket.service
```
