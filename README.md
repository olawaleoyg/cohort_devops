# LEMP Stack Deployment on Ubuntu
## Project Overview
This project demonstrates the setup and configuration of a LEMP stack (Linux, Nginx, MySQL, PHP) on an Ubuntu server.
The goal was to deploy a simple PHP web application, verify dynamic content rendering, and test server connectivity.

## Tech Stack
-   Linux (Ubuntu) – Base operating system
-   Nginx – Web server
-   MySQL – Database management system
-   PHP – Server-side scripting
-   SSH & Terminal – Remote configuration
-   Git & GitHub – Version control

# Project Steps
## Update and Install Packages
sudo apt update
sudo apt upgrade -y
sudo apt install nginx mysql-server php-fpm php-mysql -y

## 2.   Configure Nginx Server Block
Created a new site configuration:
sudo nano /etc/nginx/sites-available/nerdrxlemp

Added:
server {
    listen 80;
    server_name example.com www.example.com;
    root /var/www/nerdrxlemp;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}

Enabled and reloaded Nginx:
sudo ln -s /etc/nginx/sites-available/nerdrxlemp /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

## 3.   Verify Web Server
echo "Hello LEMP from $(hostname)" | sudo tee /var/www/nerdrxlemp/index.html

Accessed via: 'http://<server-public-ip>/

## 4.   Test PHP Processing
Created 'info.php' :
sudo nano /var/www/nerdrxlemp/info.php

Added: 
<?php
phpinfo();
?>

Accessed Via: 'http://<server-public-ip>/info.php

# Useful Commands
| Task               | Command                                 |
| ------------------ | --------------------------------------- |
| Check Nginx status | `sudo systemctl status nginx`           |
| Restart Nginx      | `sudo systemctl restart nginx`          |
| Restart PHP-FPM    | `sudo systemctl restart php8.2-fpm`     |
| Test Nginx config  | `sudo nginx -t`                         |
| View logs          | `sudo tail -f /var/log/nginx/error.log` |

## Outcome
-   Successfully deployed a fully functional LEMP stack.
-   Verified static and dynamic content delivery using Nginx and PHP.
-   Demonstrated Linux server configuration, permissions management, and web service setup.

