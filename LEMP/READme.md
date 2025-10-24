#   LEMP Stack Deployment on Ubuntu
##  Project Overview
This project demonstrates the setup and configuration of a LEMP stack (Linux, Nginx, MySQL, PHP) on an Ubuntu server.

The goal was to deploy a simple PHP web application, verify dynamic content rendering, and test server connectivity.

Assuming you have successfully connected to your EC2 ubuntu virtual server and created a security group with HTTP on port 80, then we can go the steps below:

##  Installing The Nginx Server
-   Update server package index - `sudo apt update && sudo apt upgrade`

-   Install nginx web server - `sudo apt install nginx`

-   Check nginx status - `sudos systemctl status nginx`

-   First, access it locally in the Ubuntu shell, running: `curl http://127.0.0.1:80`

-   Then access it with the public IP of the EC2 server - `http://<public-IP-address>:80`

The nginx default web page should display

![Screenshot of LEMP setup](LEMP/Images/nginx.png)

##  Install mysql
-   Install mysql-server - `sudo apt install mysql-server -y`

-   When the installation is finished, log into the MySQL console by typing - `sudo mysql`

-   This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. The following output is generated: `Welcome to the MySQL monitor. Commands end with ; org.
Your MySQL connection id is 11
Server version: 8.0.22–0ubuntu0.20.04.3 (Ubuntu)
Copyright © 2000, 2020, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>`

-   It’s recommended to run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system.

Before running the script, set the password for the root user, using

mysql_native_password as the default authentication method.

e.g
`mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

N.B.: ***mysql>*** comes forth automatically in the mysql window as soon as you log into mysql; hence, you only type what comes after that in this tutorial.

e.g: you input only ‘exit’ in ***mysql >*** exit below

Exit the MySQL shell with: 'mysql> exit

-   Start the interactive script by running: `sudo mysql_secure_installation`

