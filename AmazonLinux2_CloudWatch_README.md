# 📘 Amazon CloudWatch Logs on Amazon Linux 2

This guide demonstrates how to configure **Amazon CloudWatch Logs** on an **Amazon Linux 2 EC2 instance**. Students will learn how to collect and stream system logs such as `/var/log/messages` and `/var/log/secure` to CloudWatch for monitoring and analysis.

---

## 📋 Prerequisites

- AWS Free Tier account
- Amazon Linux 2 EC2 instance
- IAM Role attached to the instance with the following managed policy:
  - `CloudWatchAgentServerPolicy`

---

## 🚀 Step-by-Step Instructions

### ✅ Step 1: Launch an EC2 Instance

1. Use **Amazon Linux 2 AMI**
2. Attach an IAM Role with `CloudWatchAgentServerPolicy`
3. Allow SSH (port 22) in security group

---

### ✅ Step 2: Install the CloudWatch Agent

SSH into the EC2 instance and run:

```bash
sudo yum update -y
sudo yum install amazon-cloudwatch-agent -y
```

---

### ✅ Step 3: Create a CloudWatch Agent Config File

Create the JSON file to collect logs:

```bash
sudo tee /opt/aws/amazon-cloudwatch-agent/bin/config.json > /dev/null << 'EOF'
{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/messages",
            "log_group_name": "EC2-LogGroup",
            "log_stream_name": "{instance_id}-messages",
            "timestamp_format": "%b %d %H:%M:%S"
          },
          {
            "file_path": "/var/log/secure",
            "log_group_name": "EC2-LogGroup",
            "log_stream_name": "{instance_id}-secure",
            "timestamp_format": "%b %d %H:%M:%S"
          }
        ]
      }
    }
  }
}
EOF
```

---

### ✅ Step 4: Start the CloudWatch Agent

Run the following to start log streaming:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
    -a fetch-config \
    -m ec2 \
    -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
    -s
```

---

### ✅ Step 5: Verify in CloudWatch Console

1. Go to AWS Console → **CloudWatch → Logs → Log groups**
2. Look for the log group: `EC2-LogGroup`
3. Select log streams (e.g., `i-xxxxxxxxx-messages`)
4. View real-time log updates

---

## 🔍 Optional: Generate Logs for Testing

To create logs for demonstration:

```bash
sudo systemctl restart sshd
sudo tail -f /var/log/messages
sudo tail -f /var/log/secure
```

---

## 🧹 Cleanup (After Demo)

To stop logging and remove resources:

```bash
sudo systemctl stop amazon-cloudwatch-agent
aws logs delete-log-group --log-group-name EC2-LogGroup
```

---

## 🧠 Learning Objectives

- Understand how system logs can be centralized using CloudWatch Logs
- Learn CloudWatch Agent configuration for log collection
- Use the AWS Console to explore log groups and log streams
- Practice basic Linux logging and monitoring

---

## 📎 Additional Resources

- [CloudWatch Agent Documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html)
- [IAM Roles for EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2.html)

---
