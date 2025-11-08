# MySQL Client-Server Architecture Project

This project demonstrates a basic client-server setup using MySQL Relational Database Management System (RDBMS) on two Linux-based virtual servers.

## Project Overview

- **Server A**: MySQL Server
- **Server B**: MySQL Client
- Goal: Connect a client to a MySQL server remotely and perform SQL operations.

## Prerequisites

- Two Linux servers (e.g., Ubuntu EC2 instances in AWS)
- MySQL Server and MySQL Client software
- Basic knowledge of Linux commands and MySQL

## Setup Instructions

### 1. Update and Upgrade Servers

```bash
sudo apt update
sudo apt upgrade

2. Install MySQL Server (Server A)
sudo apt install mysql-server
sudo systemctl enable mysql

3 Install MySQL Client (Server B)
sudo apt install mysql-client

4 Configure Network
Ensure both servers are in the same subnet or virtual network.
Open TCP port 3306 on MySQL server’s Security Group.
Restrict access to the MySQL client’s IP for security.


5 Secure MySQL Installation
sudo mysql_secure_installation

6 Create User and Database on MySQL Server
CREATE DATABASE test_db;
CREATE USER 'remote_user'@'%' IDENTIFIED BY 'password';
GRANT ALL ON test_db.* TO 'remote_user'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;


7 Configure MySQL for Remote Connections
Edit /etc/mysql/mysql.conf.d/mysqld.cnf and change:
bind-address = 0.0.0.0
 

 8 Connect from MySQL Client
 mysql -h <server_a_local_ip> -u remote_user -p


Verify the connection:
SHOW DATABASES;

Outcome
Once connected, you can execute SQL queries remotely. This confirms a fully functional MySQL client-server setup.
