#   LEMP Stack Deployment on Ubuntu
##  Project Overview
This project demonstrates the setup and configuration of a LEMP stack (Linux, Nginx, MySQL, PHP) on an Ubuntu server.

The goal was to deploy a simple PHP web application, verify dynamic content rendering, and test server connectivity.

Assuming you have successfully connected to your EC2 ubuntu virtual server and created a security group with HTTP on port 80, then we can go the steps below:

##  Installing The Nginx Server
Update server package index - 'sudo apt update && sudo apt upgrade'
Install nginx web server - 'sudo apt install nginx'
Check nginx status - 'sudos systemctl status nginx'
First, access it locally in the Ubuntu shell, running: 'curl http://127.0.0.1:80'
Then access it with the public IP of the EC2 server - 'http://<public-IP-address>:80'
The nginx default web page should display
