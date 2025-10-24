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

-   This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. The following output is generated: 

`Welcome to the MySQL monitor. Commands end with ; org.
Your MySQL connection id is 11
Server version: 8.0.22–0ubuntu0.20.04.3 (Ubuntu)
Copyright © 2000, 2020, Oracle and/or its affiliates. All rights reserved.
Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>`

-   It’s recommended to run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script, set the password for the root user, using mysql_native_password as the default authentication method. e.g

`mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

N.B.: ***mysql>*** comes forth automatically in the mysql window as soon as you log into mysql; hence, you only type what comes after that in this tutorial.

e.g: you input only ‘exit’ in ***mysql >*** exit below

Exit the MySQL shell with: 'mysql> exit

Start the interactive script by running: `sudo mysql_secure_installation`

This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

Note: Enabling this feature is something of a judgment call. If enabled, passwords that don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.
Answer Y for yes, or anything else to continue without enabling.

`VALIDATE PASSWORD PLUGIN can be used to test passwords and improve security. It checks the strength of password and allows the users to set only those passwords which are secure enough. Would you like to setup VALIDATE PASSWORD plugin?
Press y|Y for Yes, any other key for No:`

If you answer “yes”, you’ll be asked to select a level of password validation. Keep in mind that if you enter 2 for the strongest level, you will receive errors when attempting to set any password that does not contain numbers, upper and lowercase letters, and special characters, or that is based on common dictionary words, **e.g., PassWord.1**.

`There are three levels of password validation policy:
LOW Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary file
Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1`

Regardless of whether you chose to set up the VALIDATE PASSWORD PLUGIN, your server will next ask you to select and confirm a password for the MySQL root user. This is not to be confused with the system root. The database root user is an administrative user with full privileges over the database system.

Even though the default authentication method for the MySQL root user dispenses with the use of a password, even when one is set, you should define a strong password here as an additional safety measure.

We’ll talk about this in a moment.

If you enabled password validation, you’ll be shown the password strength for the root password you just entered, and your server will ask if you want to continue with that password. If you are happy with your current password, enter Y for “yes” at the prompt:

`Estimated strength of the password: 100
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y`

For the rest of the questions, press **Y** and hit the **ENTER** key at each prompt. This will prompt you to change the root password, remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.