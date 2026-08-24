# AWS Auto Scaling Scheduled Scaling and Cron Job Practical

## 📌 Project Overview

This practical demonstrates how to automate tasks in AWS using:

* **Amazon EC2**
* **Auto Scaling Groups (ASG)**
* **Scheduled Scaling**
* **Cron Jobs**

The project focuses on automatically increasing or decreasing the number of EC2 instances at a specific time and creating a Linux cron job to execute a script automatically.

---

## 🛠️ AWS Services and Technologies Used

* Amazon EC2
* AWS Auto Scaling Group (ASG)
* Scheduled Actions
* Amazon Linux
* Shell Scripting
* Cron Job

---

# Part 1: Create Scheduled Scaling

## 🎯 Objective

Configure an Auto Scaling Group to automatically change the number of EC2 instances based on a scheduled time.

---

## 📋 Scheduled Scaling Configuration

Example configuration:

| Setting               | Value          |
| --------------------- | -------------- |
| Scheduled Action Name | Scale-In-Night |
| Minimum Capacity      | 1              |
| Desired Capacity      | 1              |
| Maximum Capacity      | 5              |
| Scheduled Time        | 10:00 PM       |

The Auto Scaling Group automatically changes the number of running instances according to the configured scheduled action.

---

## 🔧 Steps to Create Scheduled Scaling

### Step 1: Open EC2

Open the **AWS Management Console** and navigate to:

```text
EC2 → Auto Scaling Groups
```

### Step 2: Select the Auto Scaling Group

Select the required **Auto Scaling Group**.

### Step 3: Open Automatic Scaling

Navigate to:

```text
Automatic Scaling → Scheduled Actions
```

### Step 4: Create Scheduled Action

Click:

```text
Create Scheduled Action
```

### Step 5: Configure the Action

Enter the following details:

```text
Scheduled Action Name: Scale-In-Night
Minimum Capacity: 1
Desired Capacity: 1
Maximum Capacity: 5
```

Set the required start time:

```text
10:00 PM
```

### Step 6: Create Scheduled Scaling

Click:

```text
Create Scheduled Action
```

The scheduled scaling action will automatically execute at the configured time.

---

# Part 2: Cron Job

## 🎯 Objective

Create a shell script and schedule it using a cron job.

A cron job is used in Linux to automatically execute commands or scripts at a specified time.

---

## 📋 Cron Job Syntax

```text
* * * * * command
```

The five asterisks represent:

```text
Minute
Hour
Day of Month
Month
Day of Week
```

Example:

```text
0 10 * * * command
```

This runs the command every day at **10:00 AM**.

---

# Part 3: Create a Cron Job Script

## Step 1: Create a Script

Create a shell script using:

```bash
vim /home/ec2-user/test.sh
```

Add the following script:

```bash
#!/bin/bash

echo "Cron job executed at $(date)" >> /home/ec2-user/cron.log
```

This script writes the execution date and time into a file named:

```text
cron.log
```

---

## Step 2: Give Execute Permission

Make the script executable:

```bash
chmod +x /home/ec2-user/test.sh
```

---

## Step 3: Open Crontab

Open the cron configuration:

```bash
crontab -e
```

---

## Step 4: Schedule the Script

Add the following cron job:

```bash
0 10 * * * /home/ec2-user/test.sh
```

This schedules the script to run:

```text
Every day at 10:00 AM
```

---

## Step 5: Save and Exit

Save the crontab configuration and exit.

The cron service will automatically execute the script according to the configured schedule.

---

# 🔍 Verify the Cron Job

Check the output file:

```bash
cat /home/ec2-user/cron.log
```

Example output:

```text
Cron job executed at Sat Aug 23 10:00:00 AM IST 2026
```

---

# 📊 Expected Result

After completing this practical:

* Scheduled scaling automatically changes the EC2 instance capacity.
* Auto Scaling Groups can scale instances based on a configured schedule.
* A shell script can be executed automatically using Cron.
* The cron job writes execution details into a log file.
* Manual execution of repetitive tasks is reduced.

---

# 🎓 Key Learnings

* How to configure **AWS Auto Scaling Scheduled Actions**
* How to automatically scale EC2 instances at a specific time
* Understanding **minimum, desired, and maximum capacity**
* Understanding Linux **Cron Job syntax**
* Creating and executing shell scripts automatically
* Redirecting command output to a log file
* Automating repetitive server tasks

---

# 📸 Screenshots

Add your practical screenshots here:

```text
images/
├── scheduled-scaling-configuration.png
├── scheduled-action-created.png
├── cron-script.png
├── crontab-configuration.png
└── cron-job-result.png
```

---

# 🏁 Conclusion

This practical demonstrated how to automate both AWS infrastructure and Linux server tasks. AWS Auto Scaling Scheduled Actions were used to automatically control the number of EC2 instances at a specified time, while Cron Jobs were used to schedule and execute shell scripts automatically.

These automation techniques help reduce manual effort and make infrastructure management more efficient.
