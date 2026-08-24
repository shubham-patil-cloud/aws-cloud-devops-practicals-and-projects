# PHP Dynamic Website using LEMP Stack on AWS EC2

A hands-on deployment project demonstrating a **PHP dynamic website** hosted on AWS EC2 using the **LEMP stack**.

## 🚀 What is LEMP?

LEMP stands for:

```text
L → Linux
E → Nginx
M → MySQL / MariaDB
P → PHP
```

Architecture:

```text
User Browser
     ↓
AWS EC2
     ↓
Nginx
     ↓
PHP-FPM
     ↓
PHP Application
     ↓
MariaDB
```

## 🛠️ Technologies Used

- AWS EC2
- Amazon Linux
- Nginx
- MariaDB
- PHP
- PHP-FPM
- MySQLi / PHP database connectivity
- HTML
- Bash / Linux commands

## 📋 Prerequisites

- AWS account
- Amazon Linux EC2 instance
- SSH access
- HTTP port 80 allowed
- Basic Linux and PHP knowledge

## 1. Install LEMP Components

Update packages:

```bash
sudo yum update -y
```

Install Nginx, MariaDB and PHP:

```bash
sudo yum install nginx mariadb105-server php php-fpm -y
```

Start services:

```bash
sudo systemctl start nginx
sudo systemctl start mariadb
sudo systemctl start php-fpm
```

Enable at boot:

```bash
sudo systemctl enable nginx
sudo systemctl enable mariadb
sudo systemctl enable php-fpm
```

Check status:

```bash
sudo systemctl status nginx
sudo systemctl status mariadb
sudo systemctl status php-fpm
```

## 2. Test Nginx

Open:

```text
http://EC2-PUBLIC-IP
```

The Nginx welcome page should appear.

## 3. Create the Frontend

Nginx's default web root on Amazon Linux may be:

```bash
/usr/share/nginx/html
```

Example:

```bash
cd /usr/share/nginx/html
```

Create the frontend:

```bash
sudo vim signup.html
```

Create the PHP backend:

```bash
sudo vim submit.php
```

The PHP file can receive form data and store it in the database.

## 4. Create the Database

Open MariaDB:

```bash
sudo mysql -u root -p
```

Create a database:

```sql
CREATE DATABASE FCT;
```

Use it:

```sql
USE FCT;
```

Create the users table:

```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    email VARCHAR(100),
    website VARCHAR(500),
    comment VARCHAR(500),
    gender VARCHAR(100)
);
```

Check the table:

```sql
SELECT * FROM users;
DESC users;
```

Exit:

```sql
EXIT;
```

## 5. Configure PHP Database Connectivity

Install the PHP MySQL/MariaDB extension if required by the application:

```bash
sudo yum install php-mysqlnd -y
```

Restart the services:

```bash
sudo systemctl restart nginx
sudo systemctl restart php-fpm
```

Your PHP application should connect to MariaDB using the configured database name, user and password.

## 6. Test the Application

Open:

```text
http://EC2-PUBLIC-IP/signup.html
```

Enter the required information and submit the form.

Then verify the database:

```bash
sudo mysql -u root -p
```

```sql
USE FCT;
SELECT * FROM users;
```

## 🔐 AWS Security Group

Allow HTTP:

```text
Type: HTTP
Port: 80
Source: 0.0.0.0/0
```

For SSH, use your own IP instead of opening port 22 to everyone.

## 📌 Learning Outcomes

- LEMP architecture
- Nginx web server configuration
- PHP-FPM
- PHP dynamic websites
- MariaDB database creation
- SQL table creation
- PHP-to-database connectivity
- HTML form handling
- AWS EC2 deployment
- Linux service management

## ⚠️ Security Notes

Do not hard-code or publish:

- Database passwords
- AWS credentials
- SSH private keys
- Production secrets

Use environment variables or a protected configuration file for sensitive values.

## 👨‍💻 Author

**Shubham Patil**

B.Tech CSE Student | Aspiring AWS Cloud Engineer | Cloud & DevOps Enthusiast
