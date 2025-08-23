# AWS Resource Tracker Script

This project contains a **Shell Script** that helps you quickly track and list AWS resources such as **S3 Buckets, Lambda Functions, IAM Users, and EC2 Instances**.
It can also describe a **specific EC2 Instance** if you provide an `Instance ID`.

---

## 📌 Prerequisites

Before running the script, ensure you have:

1. **AWS CLI Installed**
   Install AWS CLI v2:

   ```bash
   sudo apt-get update
   sudo apt-get install awscli -y
   ```

2. **Configured AWS CLI**
   Run the following and provide your AWS credentials (Access Key, Secret Key, and Region):

   ```bash
   aws configure
   ```

3. **Permissions Required**
   The IAM user running this script should have permissions for:

   * `s3:ListAllMyBuckets`
   * `lambda:ListFunctions`
   * `iam:ListUsers`
   * `ec2:DescribeInstances`

---

## 📂 Project Setup

1. Clone or copy the script into a file:

   ```bash
   nano aws_resource_tracker.sh
   ```

2. Paste the script content:

   ```bash
   #!/bin/bash
   # AWS Resource Tracker Script

   echo "============================"
   echo " AWS Resource Tracker Script "
   echo "============================"

   # List all S3 Buckets
   echo -e "\nListing all S3 Buckets..."
   aws s3 ls

   # List all Lambda Functions
   echo -e "\nListing all Lambda Functions..."
   aws lambda list-functions

   # List all IAM Users
   echo -e "\nListing all IAM Users..."
   aws iam list-users

   # List all EC2 Instances
   echo -e "\nListing all EC2 Instances..."
   aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name,InstanceType,PublicIpAddress]' --output table

   # Describe a specific EC2 Instance if Instance ID is given
   if [ "$1" != "" ]; then
     echo -e "\nDescribing EC2 Instance with ID: $1"
     aws ec2 describe-instances --instance-ids $1 --output table
   else
     echo -e "\nNo specific EC2 Instance ID provided."
   fi

   echo -e "\n============================"
   echo " Script Execution Completed! "
   echo "============================"
   ```

3. Make the script executable:

   ```bash
   chmod +x aws_resource_tracker.sh
   ```

---

## ▶️ Usage

1. Run the script without parameters (lists all resources):

   ```bash
   ./aws_resource_tracker.sh
   ```

2. Run the script with an **EC2 Instance ID** to get details of a specific instance:

   ```bash
   ./aws_resource_tracker.sh i-0123456789abcdef
   ```

---

## 🛠 Example Output

```bash
============================
 AWS Resource Tracker Script 
============================

Listing all S3 Buckets...
2025-08-23 10:15:22 my-first-bucket
2025-08-23 10:20:11 project-logs-bucket

Listing all Lambda Functions...
{
    "Functions": [
        {
            "FunctionName": "processImage",
            "Runtime": "python3.9",
            "LastModified": "2025-08-23T10:10:22.000+0000"
        }
    ]
}

Listing all IAM Users...
{
    "Users": [
        {
            "UserName": "AdminUser",
            "UserId": "AID12345",
            "CreateDate": "2025-08-20T12:00:00+00:00"
        }
    ]
}

Listing all EC2 Instances...
-----------------------------------------------------
|                 DescribeInstances                 |
+-------------------+-----------+----------+---------+
|  i-0123456789abc  |  running  |  t2.micro|  3.3.3.3|
-----------------------------------------------------

No specific EC2 Instance ID provided.

============================
 Script Execution Completed! 
============================
```

---

## 📌 Notes

* To enable **debug mode** and see each command executed, add this line at the top of the script:

  ```bash
  set -x
  ```
* You can schedule this script with **cron jobs** for periodic AWS resource tracking.

---
