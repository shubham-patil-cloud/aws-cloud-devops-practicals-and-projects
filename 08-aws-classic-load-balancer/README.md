# AWS Classic Load Balancer Practical

## 📌 Project Overview

This project demonstrates how to create and configure an **AWS Classic Load Balancer (CLB)** to distribute incoming web traffic across multiple Amazon EC2 instances.

In this practical, three EC2 instances are launched and configured with Apache HTTP Server. Each instance displays its own hostname on a web page. A Classic Load Balancer is then created to distribute requests among the three instances.

---

## 🎯 Objective

* Launch multiple EC2 instances
* Configure Apache HTTP Server on each instance
* Create a Classic Load Balancer
* Add EC2 instances to the load balancer
* Configure Internet-facing load balancer settings
* Access the application using the Load Balancer DNS name
* Verify traffic distribution across multiple instances
* Clean up AWS resources

---

## 🛠️ AWS Services Used

* Amazon EC2
* Elastic Load Balancing – Classic Load Balancer
* Amazon VPC
* Amazon Linux

---

## 🖥️ Architecture

```text
                    Internet
                        |
                        ▼
              Classic Load Balancer
                        |
          ┌─────────────┼─────────────┐
          ▼             ▼             ▼
        EC2-1         EC2-2         EC2-3
          |             |             |
          └────── Apache Web Servers ─┘
```

---

# 1️⃣ Launch 3 EC2 Instances

Go to:

```text
AWS Console → EC2 → Launch Instances
```

Launch **3 EC2 instances**.

---

## EC2 User Data

Use the following script in the **User Data** section:

```bash
#!/bin/bash

yum update -y
yum install httpd -y

systemctl start httpd
systemctl enable httpd

echo "<h1>Hello World - $(hostname)</h1>" > /var/www/html/index.html
```

### Explanation

* `yum update -y` → Updates the system packages
* `yum install httpd -y` → Installs Apache HTTP Server
* `systemctl start httpd` → Starts Apache
* `systemctl enable httpd` → Enables Apache to start automatically after reboot
* `$(hostname)` → Displays the hostname of the EC2 instance

---

## 📊 Number of Instances

```text
Number of Instances → 3
```

Click:

```text
Launch Instances
```

---

## EC2 Instances

After launching, three web servers will be available:

```text
EC2-1 → Web Server
EC2-2 → Web Server
EC2-3 → Web Server
```

Each instance displays its own hostname when accessed.

---

# 2️⃣ Create a Classic Load Balancer

Navigate to:

```text
AWS Console → EC2 → Load Balancers → Create Load Balancer
```

Select:

```text
Classic Load Balancer
```

Click:

```text
Create
```

---

# 3️⃣ Configure the Load Balancer

Provide a name for the Load Balancer.

Example:

```text
Load Balancer Name: classic-lb-server-1
```

---

## 🌐 Scheme

Select:

```text
Internet-facing
```

This allows users on the internet to access the web application.

---

## 🏗️ Network

Select:

```text
VPC Network
```

---

## 📍 Availability Zones

Select the Availability Zones that contain the EC2 instances.

You can select all the Availability Zones used by your EC2 instances.

---

# 4️⃣ Add EC2 Instances

Select the EC2 instances:

```text
EC2 → Instances → Select all 3 instances
```

Then click:

```text
Confirm → Create Load Balancer
```

The final architecture becomes:

```text
                    Internet
                        |
                        ▼
              Classic Load Balancer
                        |
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
      EC2-1           EC2-2           EC2-3
```

---

# 5️⃣ Test the Load Balancer

Open:

```text
EC2 → Load Balancers
```

Select the created Classic Load Balancer.

Copy the:

```text
DNS Name
```

Paste the DNS name into a web browser.

Example output:

```text
Hello World - ip-10-0-1-101
```

Refresh the browser multiple times.

You may receive responses from different EC2 instances:

```text
Hello World - ip-10-0-1-101

Hello World - ip-10-0-2-202

Hello World - ip-10-0-3-303
```

---

## 📊 Result

The Classic Load Balancer distributes incoming requests among the available EC2 instances.

```text
Request 1 → EC2-1
Request 2 → EC2-2
Request 3 → EC2-3
```

This demonstrates how a Load Balancer improves:

* High Availability
* Scalability
* Traffic Distribution
* Fault Tolerance

---

# 6️⃣ Delete Resources

After completing the practical, delete the resources to avoid unnecessary AWS charges.

---

## Delete the Load Balancer

Go to:

```text
EC2 → Load Balancers
```

Select:

```text
Classic Load Balancer
```

Then:

```text
Actions → Delete Load Balancer
```

---

## Terminate EC2 Instances

Go to:

```text
EC2 → Instances
```

Select all EC2 instances.

Then:

```text
Instance State → Terminate Instance
```

---

# 📚 Key Learning Outcomes

After completing this practical, you will understand:

* How to launch multiple EC2 instances
* How to install Apache HTTP Server using User Data
* How to create a Classic Load Balancer
* How to configure an Internet-facing Load Balancer
* How to attach multiple EC2 instances
* How a Load Balancer distributes traffic
* How to test a Load Balancer using its DNS name
* How to clean up AWS resources

---

# 🏁 Conclusion

In this project, we successfully created an **AWS Classic Load Balancer** and connected it with **three EC2 web server instances**.

The Load Balancer distributed incoming traffic across the available EC2 instances, demonstrating the importance of load balancing for building **highly available, scalable, and fault-tolerant applications**.

---

## 👨‍💻 Author

**Shubham Patil**

**Project:** AWS Classic Load Balancer Practical
