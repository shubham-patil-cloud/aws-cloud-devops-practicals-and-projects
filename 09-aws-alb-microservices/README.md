# AWS Application Load Balancer Microservices Practical

## 📌 Project Overview

This project demonstrates how to deploy multiple microservices using **AWS Application Load Balancer (ALB)** and **path-based routing**.

In this practical:

* 6 EC2 instances are launched
* Apache HTTP Server is installed on each instance
* Three microservices are created:

  * Home Service
  * Mobile Service
  * Laptop Service
* Each service has its own Target Group
* An Application Load Balancer distributes requests based on URL paths

---

## 🎯 Objective

The objective of this project is to:

* Launch 6 EC2 instances
* Configure two EC2 instances for each microservice
* Install Apache HTTP Server using User Data
* Create three Target Groups
* Create an Internet-facing Application Load Balancer
* Configure path-based routing rules
* Test the ALB using different URL paths
* Check target health
* Clean up all AWS resources

---

## 🛠️ AWS Services Used

* Amazon EC2
* Elastic Load Balancing – Application Load Balancer
* Target Groups
* Amazon VPC
* Security Groups

---

# 🏗️ Architecture

```text
                              Internet
                                 |
                                 ▼
                     Application Load Balancer
                                 |
          ┌──────────────────────┼──────────────────────┐
          │                      │                      │
          ▼                      ▼                      ▼
       /home                  /mobile                /laptop
          │                      │                      │
          ▼                      ▼                      ▼
       Home-TG                Mobile-TG              Laptop-TG
          │                      │                      │
     ┌────┴────┐            ┌────┴────┐            ┌────┴────┐
     ▼         ▼            ▼         ▼            ▼         ▼
  Home-1    Home-2       Mobile-1  Mobile-2     Laptop-1  Laptop-2
```

---

# 1️⃣ Launch 6 EC2 Instances

Launch **6 EC2 instances**.

The instances are divided into three service pairs:

```text
Home-1
Home-2

Mobile-1
Mobile-2

Laptop-1
Laptop-2
```

Use the same VPC and suitable subnets.

Also configure a Security Group that allows HTTP traffic on port `80`.

---

## 📊 Service Architecture

| Path      | Target Group | EC2 Instances      |
| --------- | ------------ | ------------------ |
| `/home`   | Home-TG      | Home-1, Home-2     |
| `/mobile` | Mobile-TG    | Mobile-1, Mobile-2 |
| `/laptop` | Laptop-TG    | Laptop-1, Laptop-2 |

---

# 2️⃣ Install and Configure Apache

Use **Amazon Linux** and install Apache HTTP Server on each EC2 instance.

Run the following commands:

```bash
sudo yum update -y
sudo yum install httpd -y

sudo systemctl start httpd
sudo systemctl enable httpd
```

---

# 🏠 Configure Home Service

Create the required directory:

```bash
sudo mkdir -p /var/www/html/home
```

Create the Home Service page:

```bash
echo "<h1>Home Service - $(hostname)</h1>" | sudo tee /var/www/html/home/index.html
```

Apply this configuration on:

```text
Home-1
Home-2
```

---

# 📱 Configure Mobile Service

Create the required directory:

```bash
sudo mkdir -p /var/www/html/mobile
```

Create the Mobile Service page:

```bash
echo "<h1>Mobile Service - $(hostname)</h1>" | sudo tee /var/www/html/mobile/index.html
```

Apply this configuration on:

```text
Mobile-1
Mobile-2
```

---

# 💻 Configure Laptop Service

Create the required directory:

```bash
sudo mkdir -p /var/www/html/laptop
```

Create the Laptop Service page:

```bash
echo "<h1>Laptop Service - $(hostname)</h1>" | sudo tee /var/www/html/laptop/index.html
```

Apply this configuration on:

```text
Laptop-1
Laptop-2
```

---

## 🧪 Test Each EC2 Instance

Before creating the Application Load Balancer, test each EC2 instance using its Public IP address.

Example:

```text
http://<EC2-Public-IP>/home
http://<EC2-Public-IP>/mobile
http://<EC2-Public-IP>/laptop
```

Each instance should display its service name and hostname.

---

# 3️⃣ Create Target Group – Home

Navigate to:

```text
EC2 → Load Balancing → Target Groups → Create Target Group
```

Configure the Target Group as follows:

```text
Target Type: Instances
Target Group Name: Home-TG
Protocol: HTTP
Port: 80
Health Check Path: /home
```

Click **Next**.

Register the following instances:

```text
Home-1
Home-2
```

Include the instances as pending targets and create the Target Group.

---

# 4️⃣ Create Target Groups – Mobile and Laptop

Repeat the same process for the other services.

## Mobile Target Group

```text
Target Group Name: Mobile-TG
Health Check Path: /mobile
```

Register:

```text
Mobile-1
Mobile-2
```

---

## Laptop Target Group

```text
Target Group Name: Laptop-TG
Health Check Path: /laptop
```

Register:

```text
Laptop-1
Laptop-2
```

---

## 📊 Target Group Configuration

| Target Group | Path            | Registered Instances |
| ------------ | --------------- | -------------------- |
| Home-TG      | `/` or `/home/` | Home-1, Home-2       |
| Mobile-TG    | `/mobile/`      | Mobile-1, Mobile-2   |
| Laptop-TG    | `/laptop/`      | Laptop-1, Laptop-2   |

---

# 5️⃣ Create the Application Load Balancer

Navigate to:

```text
EC2 → Load Balancing → Load Balancers → Create Load Balancer
```

Select:

```text
Application Load Balancer
```

Configure the ALB:

```text
Load Balancer Name: Microservices-ALB
Scheme: Internet-facing
IP Address Type: IPv4
```

Select all required Availability Zones.

---

## Security Group

Configure a Security Group that allows:

```text
HTTP → Port 80
```

from:

```text
Anywhere (0.0.0.0/0)
```

Create an HTTP listener:

```text
Protocol: HTTP
Port: 80
```

Complete the creation process.

Check the Load Balancer status until it becomes:

```text
Active
```

---

# 6️⃣ Configure Path-Based Routing

Open the Application Load Balancer.

Navigate to:

```text
Listeners and Rules → HTTP:80 → View/Edit Rules
```

Create the following rules.

---

## Rule 1 – Home Service

```text
Condition:
Path → / or /home or /home/*

Priority:
1

Forward To:
Home-TG
```

---

## Rule 2 – Mobile Service

```text
Condition:
Path → /mobile or /mobile/*

Priority:
2

Forward To:
Mobile-TG
```

---

## Rule 3 – Laptop Service

```text
Condition:
Path → /laptop or /laptop/*

Priority:
3

Forward To:
Laptop-TG
```

---

## 📊 Routing Rules

| Path Condition   | Priority | Forward To |
| ---------------- | -------: | ---------- |
| `/` or `/home/*` |        1 | Home-TG    |
| `/mobile/*`      |        2 | Mobile-TG  |
| `/laptop/*`      |        3 | Laptop-TG  |

---

# 7️⃣ Test the Application Load Balancer

Copy the **ALB DNS Name** and test the following URLs.

## Home Service

```text
http://<ALB-DNS-NAME>/home
```

Expected output:

```text
Home Service - <hostname>
```

---

## Mobile Service

```text
http://<ALB-DNS-NAME>/mobile
```

Expected output:

```text
Mobile Service - <hostname>
```

---

## Laptop Service

```text
http://<ALB-DNS-NAME>/laptop
```

Expected output:

```text
Laptop Service - <hostname>
```

Refresh the browser multiple times to verify that requests are distributed between the two instances in each Target Group.

---

# 8️⃣ Check Target Health

Navigate to:

```text
EC2 → Target Groups
```

Select each Target Group.

Open:

```text
Targets
```

Confirm that all registered instances show:

```text
Healthy
```

If a target is unhealthy, check:

* Web server status
* Security Group rules
* VPC and subnet configuration
* Health check path
* Registered EC2 instance configuration

---

# 🧹 Cleanup

After completing the practical, delete the resources to avoid unnecessary AWS charges.

## 1. Delete the Application Load Balancer

```text
EC2 → Load Balancers → Select ALB → Actions → Delete
```

## 2. Delete Target Groups

Delete:

```text
Home-TG
Mobile-TG
Laptop-TG
```

## 3. Terminate EC2 Instances

Terminate all six instances:

```text
Home-1
Home-2
Mobile-1
Mobile-2
Laptop-1
Laptop-2
```

---

# 📚 Key Learning Outcomes

After completing this practical, you will understand:

* How to launch multiple EC2 instances
* How to deploy Apache web applications
* How to create multiple microservices
* How to create Target Groups
* How to register EC2 instances with Target Groups
* How to create an Application Load Balancer
* How to configure path-based routing
* How to perform health checks
* How ALB distributes traffic among multiple EC2 instances

---

# 🏁 Conclusion

In this project, we successfully deployed a **microservices architecture using AWS Application Load Balancer**.

Six EC2 instances were used to host three different services: **Home, Mobile, and Laptop**. Each service was configured with its own Target Group containing two EC2 instances.

The Application Load Balancer was configured with **path-based routing**, allowing requests to be forwarded to the appropriate service based on the URL path.

```text
/home   → Home-TG
/mobile → Mobile-TG
/laptop → Laptop-TG
```

This practical demonstrates how AWS Application Load Balancer can be used to build **scalable, highly available, and organized microservices-based applications**.

---

## 👨‍💻 Author

**Shubham Patil**

**Project:** AWS Application Load Balancer Microservices Practical
