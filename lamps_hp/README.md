# LAMP Stack Implementation on AWS (Linux, Apache, MySQL, PHP)

This project demonstrates how to set up and configure a complete **LAMP stack** on an **AWS EC2 Ubuntu server**.  
The LAMP stack — Linux, Apache, MySQL, and PHP — is a popular open-source web development environment used to host dynamic websites and web applications.

---

## 📘 Overview

The project walks through the following setup:
1. **Apache** installation and configuration as the web server  
2. **MySQL** installation for database management  
3. **PHP** installation for backend scripting  
4. **Virtual Host** configuration in Apache to host your web project  
5. **PHP Test Page** creation to verify the stack setup  

---

## 🧰 Prerequisites

Before starting, make sure you have:
- An active **AWS account**
- A running **EC2 instance** (Ubuntu)
- **SSH access** to your instance
- Basic knowledge of Linux terminal commands

---

## ⚙️ Steps Summary

### 1. Update and Upgrade Packages
```bash
sudo apt update && sudo apt upgrade -y

## 2. Install Apache Web Server
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2


## 3. Install MySQL
sudo apt install mysql-server -y
sudo mysql_secure_installation

## 4. Login to MySQL:
sudo mysql


## 5. 4. Install PHP and Modules
sudo apt install php libapache2-mod-php php-mysql -y
php --version


## 6. Configure Virtual Host
sudo mkdir /var/www/html/project-lamp
sudo chown -R $USER:$USER /var/www/html/project-lamp
sudo vi /etc/apache2/sites-available/project-lamp.conf


## 7. Create a Test PHP File
sudo vi /var/www/html/project-lamp/index.php


## 8. Add
<?php
phpinfo();
?>
