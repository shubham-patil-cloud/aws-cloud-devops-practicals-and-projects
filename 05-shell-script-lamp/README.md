# LAMP Stack Installation Using Bash Shell Script

A Linux automation project that uses a **Bash shell script** to install and configure a LAMP-style web stack on an Amazon Linux EC2 instance.

## 🚀 Project Overview

The shell script automates multiple Linux commands instead of executing each command manually.

```text
AWS EC2
   ↓
Amazon Linux
   ↓
Bash Shell Script
   ↓
Package Update
   ↓
Apache + MariaDB + PHP
   ↓
Enable Services
   ↓
Create Test Web Page
   ↓
Browser
```

## 🛠️ Technologies Used

- AWS EC2
- Amazon Linux
- Bash / Shell Scripting
- Apache HTTP Server
- MariaDB
- PHP
- Linux systemd
- Git / GitHub

## 📋 Prerequisites

- AWS account
- Amazon Linux EC2 instance
- SSH access
- Security Group allowing HTTP (TCP 80)
- Basic Linux command knowledge

## 1. Create the Shell Script

Create a file:

```bash
vim myfile.sh
```

Add:

```bash
#!/bin/bash

sudo yum update -y
sudo yum install httpd mariadb105-server php -y

sudo systemctl enable httpd
sudo systemctl enable mariadb
```

You can extend the script with additional application configuration as required.

## 2. Shebang

The first line:

```bash
#!/bin/bash
```

tells Linux to execute the script using the Bash shell.

## 3. Run the Script

You can execute a script with:

```bash
sudo bash myfile.sh
```

Or make it executable:

```bash
chmod +x myfile.sh
./myfile.sh
```

## 4. Test Apache

Check Apache:

```bash
sudo systemctl status httpd
```

Start it if required:

```bash
sudo systemctl start httpd
```

Enable it at boot:

```bash
sudo systemctl enable httpd
```

Test from a browser:

```text
http://EC2-PUBLIC-IP
```

## 🔐 AWS Security Group

Allow HTTP:

```text
Type: HTTP
Port: 80
Source: 0.0.0.0/0
```

## 📌 Learning Outcomes

- Bash scripting fundamentals
- Shebang and script execution
- Linux package automation
- Apache installation
- MariaDB installation
- PHP installation
- systemd service management
- AWS EC2 deployment
- Automating repetitive server setup

## ⚠️ Note

Package names can differ between Amazon Linux versions. Verify available packages with:

```bash
yum search <package-name>
```

## 👨‍💻 Author

**Shubham Patil**

B.Tech CSE Student | Aspiring AWS Cloud Engineer | Cloud & DevOps Enthusiast
