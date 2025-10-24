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

-   When you’re finished, test if you’re able to log in to the MySQL console by typing: `sudo mysql -p`
Notice the -p flag in this command, which will prompt you for the password used after changing the root user password.

-   To exit the MySQL console, type: `mysql> exit`

Notice that you need to provide a password to connect as the root user.

For increased security, it’s best to have dedicated user accounts with less expansive privileges set up for every database, especially if you plan on having multiple databases hosted on your server.

The MySQL server is now installed and secured.

Next, I installed PHP, the final component in the LAMP stack.

## Install PHP

Nginx has been installed to serve your content and MySQL installed to store and manage data.

Now , you will install PHP to process code and generate dynamic content for the web server.

While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration.

You will need to install **php-fpm**, which stands for **PHP fastCGI process manager**, and tell Nginx to pass PHP requests to this software for processing.

Additionally, you will need **php-mysql** , a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

- To install these two packages at once, run: `sudo apt install php-fpm php-mysql -y`

## Setting up nginx to use a PHP Processor

When using the Nginx web server, we can create **server blocks** (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. Here,I will use maxxim.tech as my domain name and nerdrxlemp as a subdomain.

On Ubuntu 22.04, Nginx has one server block enabled by default and is configured to serve documents out of a directory at /var/www/html.

While this works well for a single site, it can become difficult to manage if we are hosting multiple sites. Instead of modifying /var/www/html, we’ll create a directory structure within /var/www for the your_domain website, leaving /var/www/html in place as the default directory to be served if a client request does not match any other sites.

-   Create the root web directory for ‘your_domain’ as follows: 
`sudo mkdir /var/www/nerdrxlemp`

-   Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user: 
`sudo chown -R $USER:$USER /var/www/nerdrxlemp`

-   Then, open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. But before we do that first find the correct **fpm socket**:
`ls /var/run/php/` you would see something like `php8.3-fpm.sock`


Here, I use vi: 
`sudo vi /etc/nginx/sites-available/nerdrxlemp`

-   This will create a new blank file. Paste in the following bare-bones configuration:
`#/etc/nginx/sites-available/nerdrxlemp
server {
listen 80;
server_name maxxim.tech nerdrxlemp.maxxim.tech;
root /var/www/nerdrxlemp;
index index.html index.htm index.php;
location / {
try_files $uri $uri/ =404;
}:wq!

location ~ \.php$ {
include snippets/fastcgi-php.conf;
fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
}
location ~ /\.ht {
deny all;
}
}`

**NOTE** make sure you use the latest or correct **FPM socket**, then save and close the file

-   Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:
`sudo ln -s /etc/nginx/sites-available/nerdrxlemp /etc/nginx/sites-enabled/`

This will tell Nginx to use the configuration next time it is reloaded.

-   Next, you can test your configuration for syntax errors by typing:
`sudo nginx -t`
You shall see following message:
`nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful`

If any errors are reported, you go back to your configuration file to review its contents before continuing.

You also need to disable default Nginx host that is currently configured to listen on port 80, for this run:

```
$sudo unlink /etc/nginx/sites-enabled/default
```

-   When ready, you reload Nginx to apply the changes:

```
$sudo systemctl reload nginx
```

Your new website is now active, but the web root /var/www/nerdrxlemp is still empty.

You can create an index.html file in that location so that you can test that your new server block works as expected:

```
sudo sh -c "echo 'LEMP IS LIVE from hostname ' $(hostname) ' with local IP ' $(hostname -I | awk '{print $1}') > /var/www/nerdrxlemp/index.html"

```

Thereafter, you access the website URL using IP address or domain name:

```
http://<Public-IP-Address>:80
or
http://<Public-DNS-Name>:80
```

N.B:

- With or without :80, it works, since it listens on port 80 which is the HTTP port.
- If you see the text from ‘echo’ command you wrote to index.html file, then it means your Nginx site is working as expected.

In the output you will see your server’s public hostname (DNS name) and public IP address.

You can leave this file in place as a temporary landing page for your application until you set up an index.php file to replace it.

Once that is done, remember to remove or rename the index.html file from your document root, as it would take precedence over an index.php file by default.

The LEMP stack is now fully configured.

In the next step, you will create a PHP script to test that Nginx is in fact able to handle php files within the newly configured website.

## TESTING PHP WITH NGINX

Your LEMP stack should now be completely set up.

At this point, your LEMP stack is completely installed and fully operational.

You can test it to validate that Nginx can correctly hand ‘.php files’ off to the PHP processor.

You can do this by creating a test PHP file in our document root, opening a new file called info.php within our document root.

```
$sudo nano /var/www/nerdrxlemp/info.php
```

Type or paste the following lines into the new file. This is a valid PHP code that will return information about your server:

```
<?php
phpinfo();
```

You can now access this page in your web browser by visiting the domain name or public IP address we’ve set up in our Nginx configuration file, followed by /info.php:

```
http://`server_domain_or_IP`/info.php
```

A web page with detailed information about your server is displayed:

![Screenshot of LEMP setup](LEMP/Images/PHP.png)

After checking the relevant information about your PHP server through that page, it’s best to remove the file created as it contains sensitive information about the PHP environment and Ubuntu server.
`sudo rm /var/www/our_domain/info.php`

##  Retrieving data from mysql database with PHP

In this step you will create a test database (DB) with simple “To do list” and configure access to it, so the Nginx website would be able to query data from the DB and display it.

You will need to create a new user with the **mysql_native_password authentication method** in order to be able to connect to the MySQL database from PHP.

You will create a database named nerdrx_database and a user named nerdrx.

First, connect to the MySQL console using the root account:

```
$sudo mysql
```

To create a new database, run the following command from your MySQL console:

```
mysql> CREATE DATABASE `nerdrx_database`;
```

N.B: **mysql>** is the provided by mysql after logging into mysql, hence do not type it out

Now we can create a new user and grant him full privileges on the database you have just created.

The following command creates a new user named nerdrx, using mysql_native_password as default authentication method. One can define this user’s password with whatever one wishes by replacing with an actual password

```
mysql> CREATE USER 'nerdrx'@'%' IDENTIFIED WITH mysql_native_password BY '******';

```

Replace ***** with a real password

Now, you need to give this user permission over the example_database database thus:

```
mysql> GRANT ALL ON nerdrx_database.* TO 'nerdrx'@'%';
```

This will give ‘nerdrx’ full privileges over the nerdrx_database database, while preventing this user from creating or modifying other databases on your server.

Now exit the MySQL shell with:

```
mysql> exit
```

You can test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials:

```
$mysql -u nerdrx -p
```

The -u specifies the user and the -p flag in this command will prompt you for the password used when creating the nerdrx user. After logging in to the MySQL console, confirm that you have access to the nerdrx_database database:

```
mysql> SHOW DATABASES;
```

This will give the following output:

```
Output
+ - - - - - - - - - - +
| Database |
+ - - - - - - - - - - +
| information_schema |
| nerdrx_database |
| performance_schema |
+ - - - - - - - - - - +
2 rows in set (0.000 sec)
```

Next, is to create a test table named todo_list.

From the MySQL console, run the following statement:

```
mysql> CREATE TABLE nerdrx_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));
```

And insert a few rows of content in the test table. We might want to repeat the next command a few times, using different VALUES:

```
mysql> INSERT INTO nerdrx_database.todo_list (content) VALUES ("GitOps with FluxCD");
mysql> INSERT INTO nerdrx_database.todo_list (content) VALUES ("Python for Entry-Level (PCP-302)");
mysql> INSERT INTO nerdrx_database.todo_list (content) VALUES ("Helm-Chart for Kubernetes ");
mysql> INSERT INTO nerdrx_database.todo_list (content) VALUES ("GitOps with ArgoCD");
mysql> INSERT INTO nerdrx_database.todo_list (content) VALUES ("CI/CD with Gitlab");
```

To confirm that the data was successfully saved to the table, run:

```
mysql> SELECT * FROM nerdrx_database.todo_list;mysql> SELECT * FROM nerdrx_database.todo_list;
```

The following is displayed as the output:

```
Output
+ - - - - -+ - - - - - - - - - - - - - - - - - +
| item_id | content |
+ - - - - -+ - - - - - - - - - - - - - - - - - +
| 1 | GitOps with FluxCD |
| 2 | Python for Entry-Level (PCP-302) |
| 3 | Helm-Chart for Kubernetes |
| 4 | GitOps with ArgoCD |
| 5 | CI/CD with Gitlab |
+ - - - - -+ - - - - - - - - - - - - - - - - - +
5 rows in set (0.000 sec)
```

After confirming that you have valid data in your test table, then exit the MySQL console:

```
mysql> exit
```

Now, you can create a PHP script that connects to MySQL and query for the content.

```
$nano /var/www/nerdrxlemp/todo_list.php
```

The following PHP script connects to the MySQL database and queries for the content of the todo_list table, while displaying the results in a list. If there is a problem with the database connection, it will throw an exception.

Thereafter, copy this content into the todo_list.php script:

```
<?php
$user = "nerdrx";
$password = "password";
$database = "nerdrx_database";
$table = "todo_list";
try {
$db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
echo "<h2>TODO</h2><ol>";
foreach($db->query("SELECT content FROM $table") as $row) {
echo "<li>" . $row['content'] . "</li>";
}
echo "</ol>";
} catch (PDOException $e) {
print "Error!: " . $e->getMessage() . "<br/>";
die();
}
```

Save and close the file when done editing.

You can now access this page in the web browser by visiting the domain name or public IP address configured for your website, followed by **/todo_list.php**:

```
http://<Public_domain_or_IP>/todo_list.php
```

You should see a page like this, showing the content you’ve inserted in your test table:

[PHP Served page for a Todo-list](/cohort_devops/LEMP/Images/todo_list_php.png)

That means your PHP environment is ready to connect and interact with the MySQL server.

Congratulations for staying through the whole process, you can now conveniently deploy LEMP stack on AWS EC2 Virtual machine.

##  CONCLUSION:

LEMP is a variation of the ubiquitous LAMP stack used for developing and deploying web applications. Traditionally, LAMP consists of Linux, Apache, MySQL, and PHP. Due to its modular nature, the components can easily be swapped out. With LEMP, Apache is replaced with the lightweight yet powerful Nginx. Together, they provide a proven set of software for delivering high-performance web applications.