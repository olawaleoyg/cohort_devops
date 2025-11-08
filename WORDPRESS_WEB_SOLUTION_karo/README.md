# 🧰 WordPress + MySQL Multi-Server Setup on AWS EC2 (RHEL 9 / Amazon Linux)

This guide walks through deploying **WordPress** on one EC2 instance and **MySQL** on another, running **RHEL 9** (or compatible Amazon Linux) servers.

---

## 🏗️ Architecture Overview

| Component | Purpose | Example Private IP |
|------------|----------|--------------------|
| Web Server | Hosts Apache, PHP, and WordPress | `172.31.16.76` |
| DB Server | Hosts MySQL Community Server | `172.31.18.74` |

Public access (HTTP) goes to the Web Server only.  
The Web Server connects privately to the DB Server.

---

## 🚀 1. Launch Two EC2 Instances

- **AMI**: RHEL 9 or Amazon Linux 2023  
- **Security Groups:**
  - Web Server: allow inbound `HTTP (80)` and `SSH (22)`
  - DB Server: allow inbound `MySQL (3306)` **only from Web Server’s private IP**
- Assign both to the same VPC/subnet so they can communicate via private IPs.

---

## ⚙️ 2. Install MySQL on the DB Server

```bash
sudo yum update -y
sudo yum install -y https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo yum install -y mysql-community-server
```

## Start and enable MySQL
`sudo systemctl enable --now mysqld`

## Get the temporary password
`sudo grep 'temporary password' /var/log/mysqld.log`

## Secure MySQL installation
`sudo mysql_secure_installation`

When prompted:
- Set new root password
- Remove anonymous users
- Disallow root remote login
- Remove test DB
- Reload privileges

## Create a Database and User for WordPress
Login as root:
`sudo mysql -u root -p`

Then run:

```
CREATE DATABASE wordpress;
CREATE USER 'myuser'@'%' IDENTIFIED BY 'StrongPassword123!';
GRANT ALL PRIVILEGES ON wordpress.* TO 'myuser'@'%';
FLUSH PRIVILEGES;
```
**Allow remote connections**

Edit MySQL configuration:
`sudo vi /etc/my.cnf`

Add (under `[mysqld]`):
`bind-address = 0.0.0.0`

Restart MySQL:
`sudo systemctl restart mysqld`

Ensure the firewall or security group allows port 3306 **only from** the Web Server.

## Install Apache, PHP, and WordPress on the Web Server

```
sudo yum update -y
sudo yum install -y httpd php php-mysqlnd php-fpm php-json php-gd php-xml php-mbstring wget unzip
```
Enable and start Apache:
`sudo systemctl enable --now httpd`

Download and extract WordPress:
```
cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzf latest.tar.gz
sudo chown -R apache:apache wordpress
sudo chmod -R 755 wordpress
```
## Configure WordPress
Edit the WordPress configuration file:
`sudo vi /var/www/html/wordpress/wp-config.php`

Replace these lines with your database credentials:
```
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'myuser' );
define( 'DB_PASSWORD', 'StrongPassword123!' );
define( 'DB_HOST', '172.31.18.74' );  // private IP of DB server
```
## Test the Connection
From the Web Server:
`mysql -u myuser -p -h 172.31.18.74`

If you get a mysql> prompt — connection works ✅

## Access WordPress in Browser
Visit:
`http://<your-web-server-public-ip>/wordpress`

Example:
`http://54.81.220.139/wordpress`

Complete the WordPress setup wizard:
- Site title
- Admin username & password
- Email

Then log in at:
`http://<your-public-ip>/wordpress/wp-admin`

## Post-Setup Security Tips
1. **Re-enable SELinux** if you disabled it:
`sudo setenforce 1`

2. **Restrict MySQL** access to only the web server.
3. **Enable automatic updates** for system and plugins.
4. **Use HTTPS** (with Let’s Encrypt or AWS Load Balancer).

| Error                                       | Cause                                | Fix                                      |
| ------------------------------------------- | ------------------------------------ | ---------------------------------------- |
| `Access denied for user 'root'@'localhost'` | Wrong or expired MySQL root password | Reset using `--skip-grant-tables` method |
| `Error establishing a database connection`  | Wrong DB credentials or host         | Update `wp-config.php`                   |
| `mysqld_safe: command not found`            | Different binary path                | Use `/usr/sbin/mysqld`                   |
| `Unable to lock ./ibdata1`                  | MySQL already running                | Stop existing process before restarting  |
| WordPress loads only via `curl localhost`   | Apache/Firewall not open             | Allow HTTP (80) in security group        |

![Book List Screenshot](/cohort_devops/WORDPRESS_WEB_SOLUTION_karo/Images/project%207-1.png)