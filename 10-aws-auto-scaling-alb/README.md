# AWS Auto Scaling Group with Application Load Balancer Practical

## 📌 Project Overview

This project demonstrates how to configure an **AWS Auto Scaling Group (ASG)** to automatically maintain and adjust the number of Amazon EC2 instances based on workload.

The Auto Scaling Group is integrated with an **Application Load Balancer (ALB)** so that incoming traffic can be distributed across healthy EC2 instances.

In this practical, we will:

* Create a Launch Template
* Create an Auto Scaling Group
* Configure desired, minimum, and maximum capacity
* Attach the Auto Scaling Group to a Target Group
* Configure dynamic scaling based on CPU utilization
* Create an Application Load Balancer
* Test the application using the ALB DNS name
* Monitor automatic scaling using CloudWatch metrics

---

# 🎯 Objective

The objective of this project is to understand how AWS automatically increases or decreases the number of EC2 instances according to application demand.

---

# 🛠️ AWS Services Used

* Amazon EC2
* Amazon EC2 Auto Scaling
* Application Load Balancer (ALB)
* Target Groups
* Amazon CloudWatch
* Amazon VPC
* Security Groups

---

# 📖 What is Scaling?

**Scaling** means increasing or decreasing computing resources according to application demand.

For example:

```text id="0hwbmg"
Low Traffic  → Fewer EC2 Instances
High Traffic → More EC2 Instances
```

---

# 📊 Types of Scaling

## 1️⃣ Vertical Scaling

Vertical scaling means increasing or decreasing the resources of an existing EC2 instance.

Example:

```text id="2ffaz0"
t3.micro → t3.medium
```

This increases the computing capacity of the same instance.

### Example

```text id="cj1otq"
t3.micro → t3.medium
```

---

## 2️⃣ Horizontal Scaling

Horizontal scaling means increasing or decreasing the number of EC2 instances.

Example:

```text id="4bm3wj"
2 EC2 Instances → 5 EC2 Instances
```

or:

```text id="6w6m0f"
5 EC2 Instances → 2 EC2 Instances
```

This approach is commonly used with Auto Scaling Groups and Load Balancers.

---

# 🔄 What is an Auto Scaling Group?

An **Auto Scaling Group (ASG)** automatically launches or terminates EC2 instances based on:

* Desired capacity
* Minimum capacity
* Maximum capacity
* Scaling policies

The Auto Scaling Group helps maintain application availability and automatically adjusts infrastructure according to demand.

---

# ⚙️ Important Auto Scaling Parameters

## Minimum Capacity

The minimum number of EC2 instances that must always be running.

Example:

```text id="puh60q"
Minimum Capacity: 1
```

---

## Desired Capacity

The normal or initial number of EC2 instances.

Example:

```text id="cswx10"
Desired Capacity: 2
```

---

## Maximum Capacity

The maximum number of EC2 instances that the Auto Scaling Group can launch.

Example:

```text id="bc7q9x"
Maximum Capacity: 5
```

---

# 📈 Types of Auto Scaling

## 1. Dynamic Scaling

Dynamic scaling automatically changes the number of instances according to demand.

Example:

```text id="5o9yab"
High CPU Utilization
        ↓
CloudWatch Metric
        ↓
ASG Scaling Policy
        ↓
Launch New EC2 Instance
        ↓
Register Instance with Target Group
```

Example:

```text id="00m56z"
2 Instances → 3 Instances → 4 Instances
```

---

## 2. Scheduled Scaling

Scheduled scaling changes the number of instances at a specific time.

Example:

```text id="s1ek5g"
10:00 AM → Increase Desired Capacity
10:00 PM → Decrease Desired Capacity
```

This is useful when traffic patterns are predictable.

---

# 🏗️ Project Architecture

```text id="j9yvcf"
                         Internet
                            |
                            ▼
                 Application Load Balancer
                            |
                            ▼
                      Target Group
                            |
             ┌──────────────┴──────────────┐
             ▼                             ▼
        Auto Scaling Group            Auto Scaling Group
             │
      ┌──────┼─────────────┐
      ▼      ▼             ▼
    EC2-1  EC2-2      New EC2 Instance
```

When CPU utilization increases:

```text id="cy4nh5"
CPU Load Increases
        ↓
CloudWatch Detects Metric
        ↓
Auto Scaling Policy Triggers
        ↓
New EC2 Instance Launches
        ↓
Instance Registers with Target Group
        ↓
ALB Distributes Traffic
```

---

# 1️⃣ Create a Launch Template

Navigate to:

```text id="h75z80"
EC2 → Launch Templates → Create Launch Template
```

Provide a Launch Template name.

Example:

```text id="6c07wv"
Launch Template Name: AutoScaling-Template
```

Configure the following:

```text id="yp9sn1"
AMI: Amazon Linux
Instance Type: t3.micro
Key Pair: Select existing key pair
Security Group: Allow HTTP and SSH
```

---

## 🧾 User Data

Use the following User Data script to install Apache automatically:

```bash id="x4n29p"
#!/bin/bash

yum update -y
yum install httpd -y

systemctl start httpd
systemctl enable httpd

echo "<h1>Hello World - $(hostname)</h1>" > /var/www/html/index.html
```

This script automatically:

* Updates the system
* Installs Apache HTTP Server
* Starts Apache
* Enables Apache at boot
* Displays the EC2 hostname

Click:

```text id="uz5ypq"
Create Launch Template
```

---

# 2️⃣ Create an Auto Scaling Group

Navigate to:

```text id="aq26u1"
EC2 → Auto Scaling Groups → Create Auto Scaling Group
```

Select the Launch Template created earlier.

Example:

```text id="9ctd5j"
Auto Scaling Group Name: My-ASG
```

Select:

```text id="gygjmv"
Launch Template: AutoScaling-Template
```

---

## 🌐 Network Configuration

Select:

```text id="rwbb6c"
VPC: Select your VPC
Availability Zones: Select suitable subnets
```

Selecting multiple Availability Zones improves availability.

---

# 3️⃣ Configure Group Size

Configure the Auto Scaling Group capacity.

Example:

```text id="w9esrn"
Desired Capacity: 2
Minimum Capacity: 1
Maximum Capacity: 5
```

This means:

```text id="c5wyk3"
Minimum  → 1 EC2 Instance
Desired  → 2 EC2 Instances
Maximum  → 5 EC2 Instances
```

Initially, the ASG maintains approximately two instances.

---

# 4️⃣ Configure Automatic Scaling Policy

Select:

```text id="psdlxz"
Automatic Scaling Policy: Target Tracking Scaling Policy
```

Choose:

```text id="dr8nj3"
Metric Type: Average CPU Utilization
```

Configure the target value:

```text id="6wg2u0"
Target Value: 50%
```

Configure instance warm-up:

```text id="w7p9fb"
Instance Warmup: 300 seconds
```

For the maintenance policy, use the required/default setting according to the practical configuration.

Click:

```text id="y83cuc"
Next
```

---

# 5️⃣ Add Notifications

Optionally configure notifications for Auto Scaling events.

Examples include:

```text id="fzb7ox"
Instance Launch
Instance Termination
Scaling Events
```

You can use Amazon SNS for notifications.

---

# 6️⃣ Add Tags

Add tags if required.

Example:

```text id="3y1jhh"
Name: AutoScaling-EC2
```

---

# 7️⃣ Review and Create the Auto Scaling Group

Review all configuration settings and click:

```text id="l1rd6g"
Create Auto Scaling Group
```

Repeat the process if creating additional Auto Scaling Groups for other services.

Example:

```text id="sqefbv"
Home-ASG
Mobile-ASG
Laptop-ASG
```

---

# 8️⃣ Create Target Groups

Navigate to:

```text id="9a2uiz"
EC2 → Load Balancing → Target Groups → Create Target Group
```

Configure:

```text id="v4i6bm"
Target Type: Instances
Protocol: HTTP
Port: 80
Health Check Path: /
```

Example Target Groups:

```text id="tz9nh1"
Home-TG
Mobile-TG
Laptop-TG
```

---

# 📊 Target Group Configuration

| Target Group | Path            | Registered Instances |
| ------------ | --------------- | -------------------- |
| Home-TG      | `/` or `/home/` | ASG instances        |
| Mobile-TG    | `/mobile/`      | ASG instances        |
| Laptop-TG    | `/laptop/`      | ASG instances        |

> Instances launched by an Auto Scaling Group are automatically registered with the attached Target Group.

---

# 9️⃣ Attach the Auto Scaling Group to the Load Balancer

Navigate to:

```text id="ntq8a0"
EC2 → Auto Scaling Groups → Select ASG → Actions → Edit
```

Attach the required Target Group.

Examples:

```text id="xtal4f"
Home-ASG   → Home-TG
Mobile-ASG → Mobile-TG
Laptop-ASG → Laptop-TG
```

The architecture becomes:

```text id="mwrs8d"
Home-ASG
   ↓
Home-TG
   ↓
Application Load Balancer
```

The same configuration can be used for the other services.

---

# 🔟 Create an Application Load Balancer

Navigate to:

```text id="9ljsdh"
EC2 → Load Balancing → Load Balancers → Create Load Balancer
```

Select:

```text id="wcx98r"
Application Load Balancer
```

Configure:

```text id="4idgts"
Load Balancer Name: Microservices-ALB
Scheme: Internet-facing
IP Address Type: IPv4
```

Select the required Availability Zones.

Configure a Security Group that allows:

```text id="11mv7j"
HTTP → Port 80
```

Create an HTTP listener:

```text id="8drmxt"
Protocol: HTTP
Port: 80
```

Create the Load Balancer and wait until its status becomes:

```text id="hqk7rs"
Active
```

---

# 1️⃣1️⃣ Configure Path-Based Routing

Open:

```text id="c82zaz"
ALB → Listeners and Rules → HTTP:80
```

Create the following rules.

| Path Condition   | Priority | Forward To |
| ---------------- | -------: | ---------- |
| `/` or `/home/*` |        1 | Home-TG    |
| `/mobile/*`      |        2 | Mobile-TG  |
| `/laptop/*`      |        3 | Laptop-TG  |

Example:

```text id="jm9cy1"
/home
   ↓
Home-TG
   ↓
Home ASG Instances
```

---

# 1️⃣2️⃣ Test the Application Load Balancer

Copy the ALB DNS name.

Test:

```text id="yrrxrz"
http://<ALB-DNS-NAME>/
```

If path-based routing is configured, test:

```text id="6y9k1h"
http://<ALB-DNS-NAME>/home

http://<ALB-DNS-NAME>/mobile

http://<ALB-DNS-NAME>/laptop
```

Refresh the browser multiple times to verify that the Application Load Balancer distributes requests among healthy instances.

---

# 1️⃣3️⃣ Test Auto Scaling

Generate CPU load or use a suitable workload to increase CPU utilization.

When the average CPU utilization exceeds the configured target:

```text id="i5xj2a"
High CPU Load
      ↓
CloudWatch Detects High CPU
      ↓
ASG Scaling Policy
      ↓
New EC2 Instance Launches
      ↓
New Instance Registers with Target Group
      ↓
ALB Sends Traffic to New Instance
```

Example:

```text id="d8yv0z"
2 Instances → 3 Instances → 4 Instances
```

When the workload decreases, the Auto Scaling Group can terminate unnecessary instances while respecting the configured minimum capacity.

---

# 1️⃣4️⃣ Check Target Health

Navigate to:

```text id="d90nre"
EC2 → Target Groups → Select Target Group → Targets
```

Check that instances are registered and show:

```text id="f5m0fr"
Healthy
```

If an instance is unhealthy, check:

* Apache HTTP Server status
* Security Group rules
* Health check path
* VPC and subnet configuration
* Target Group configuration
* Application response

---

# 🧹 Cleanup

After completing the practical, clean up the resources to avoid unnecessary AWS charges.

## 1. Delete Application Load Balancer

```text id="ewgg3n"
EC2 → Load Balancers → Select ALB → Delete
```

## 2. Delete Auto Scaling Groups

Delete:

```text id="9aqp1c"
Home-ASG
Mobile-ASG
Laptop-ASG
```

## 3. Delete Target Groups

Delete:

```text id="p4s8qf"
Home-TG
Mobile-TG
Laptop-TG
```

## 4. Delete Launch Template

Delete the Launch Template if it is no longer required.

---

# 📚 Key Learning Outcomes

After completing this practical, you will understand:

* What scaling is
* The difference between vertical and horizontal scaling
* How to create a Launch Template
* How to create an Auto Scaling Group
* Minimum, desired, and maximum capacity
* Dynamic scaling
* Target tracking scaling policies
* CloudWatch CPU metrics
* How ASG launches new EC2 instances automatically
* How to integrate ASG with Target Groups
* How to integrate Auto Scaling with an Application Load Balancer
* How to test automatic scaling

---

# 🏁 Conclusion

In this project, we successfully configured an **AWS Auto Scaling Group integrated with an Application Load Balancer**.

The Auto Scaling Group maintains the required number of EC2 instances and can automatically launch additional instances when CPU utilization increases.

```text id="xxmq23"
CloudWatch Metric
      ↓
Auto Scaling Policy
      ↓
Launch New EC2 Instance
      ↓
Register with Target Group
      ↓
Application Load Balancer
      ↓
User Request
```

This architecture improves:

* 🚀 Scalability
* 🔄 Automatic resource management
* 🛡️ High availability
* ⚡ Better performance during traffic spikes
* 💰 Cost optimization during lower demand

---

## 👨‍💻 Author

**Shubham Patil**

**Project:** AWS Auto Scaling Group with Application Load Balancer Practical
