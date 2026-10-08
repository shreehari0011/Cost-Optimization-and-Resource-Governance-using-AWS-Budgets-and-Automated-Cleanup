# Cost-Optimization-and-Resource-Governance-using-AWS-Budgets-and-Automated-Cleanup

This project implements an automated AWS cost optimization and governance system to reduce unnecessary cloud spending by identifying and cleaning unused resources.

# 🎯 Objectives

- Monitor AWS budget usage
- Trigger alerts on threshold breaches
- Automatically clean unused resources
- Maintain logs and reports for auditing

# 📸ARCHITECTURE IMAGE
  <img width="1536" height="1024" alt="Image" src="https://github.com/user-attachments/assets/9c2a7b35-9d82-423d-a9ab-da3bdcfb76d2" />

# 🛠️ Technologies Used
- Amazon Web Services
- AWS Budgets
- AWS Lambda
- Amazon EC2
- Amazon CloudWatch

# 📌 GitHub Repository Structure
📁 Project Structure

```

aws-cost-optimization/
│
├── lambda/
│   └── cleanup.py
│
├── screenshots/
│   ├── budget-alert.png
│   ├── cloudwatch-logs.png
│
├── docs/
│   └── architecture.md
│
├── README.md
└── requirements.txt

```

# 🧠 Cost Optimization Strategy

- Identify idle resources:
- Stopped EC2 instances
- Unattached EBS volumes
- Unused Elastic IPs
- Define lifecycle policies
- Automate cleanup using Lambda
- Track spending using budgets
  
# 🔐 Governance Approach

- Budget thresholds (80%, 100%)
- Alert notifications via email/SNS
- Automated enforcement using Lambda
- Logging for accountability

# ⚙️ Implementation Steps

STEP 1️⃣ Create AWS Budget

- Go to Billing → Budgets
- Create Cost Budget
-Set:
Monthly limit (e.g., $50)
Alerts at:
80%
100%
Configure email alerts

STEP 2️⃣ Resource Monitoring

Track:
- Stopped EC2 (older than X days)
- Unattached EBS volumes
- Unused Elastic IPs

# 3️⃣ Lambda Automation Setup

- Create Lambda function
- Attach IAM role with permissions:
- EC2 Full Access (or limited policies)
- CloudWatch Logs

# 💻 Lambda Code 
import boto3
from datetime import datetime, timezone, timedelta

ec2 = boto3.client('ec2')

def lambda_handler(event, context):
    
    # Cleanup Stopped EC2 Instances
    instances = ec2.describe_instances()
    
    for reservation in instances['Reservations']:
        for instance in reservation['Instances']:
            state = instance['State']['Name']
            
            if state == 'stopped':
                launch_time = instance['LaunchTime']
                age = datetime.now(timezone.utc) - launch_time
                
                if age > timedelta(days=7):
                    print(f"Terminating instance: {instance['InstanceId']}")
                    ec2.terminate_instances(
                        InstanceIds=[instance['InstanceId']]
                    )

    # Cleanup Unattached EBS Volumes
    volumes = ec2.describe_volumes(
        Filters=[{'Name': 'status', 'Values': ['available']}]
    )
    
    for volume in volumes['Volumes']:
        print(f"Deleting volume: {volume['VolumeId']}")
        ec2.delete_volume(VolumeId=volume['VolumeId'])

    # Release Unused Elastic IPs
    addresses = ec2.describe_addresses()
    
    for addr in addresses['Addresses']:
        if 'InstanceId' not in addr:
            print(f"Releasing Elastic IP: {addr['PublicIp']}")
            ec2.release_address(
                AllocationId=addr['AllocationId']
            )

    return "Cleanup Completed"

 # 4️⃣ CloudWatch Setup
 
- Create rule (EventBridge)
- Trigger Lambda daily
- Check logs in CloudWatch

# 📊 Output / Results

- Reduced AWS cost by cleaning idle resources
- Automated governance system
- Real-time alerts and logs

# 📸 Important screenshots

1.Budget Setup

<img width="595" height="1280" alt="Image" src="https://github.com/user-attachments/assets/d4337b3c-8eac-4a7a-a476-8a43246dd338" />

2.Identify Stopped EC2

<img width="1280" height="687" alt="Image" src="https://github.com/user-attachments/assets/2d0e3b96-3366-4d6d-9381-4db9cc01c3ff" />

3.EBS Volumes

<img width="793" height="1280" alt="Image" src="https://github.com/user-attachments/assets/b2dcb9b3-b0e4-4ff6-b996-6242a1f4e889" />

4.Elastic IP

<img width="1280" height="687" alt="Image" src="https://github.com/user-attachments/assets/5a44a14f-ad32-4e62-a159-2e0d0176ec02" />

5.EBS / Elastic IP

<img width="1188" height="1280" alt="Image" src="https://github.com/user-attachments/assets/9afa3947-35b7-415a-aaa8-88d3ead9d73d" />

6.AWS Budget Dashboard

<img width="1280" height="687" alt="Image" src="https://github.com/user-attachments/assets/b979144a-25e1-4002-9f6e-8ec63ba3234f" />



