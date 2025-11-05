# MEAN Stack Deployment to Ubuntu in AWS

This project demonstrates how to deploy a **MEAN Stack (MongoDB, Express, Angular, Node.js)** web application on an **Ubuntu EC2 instance in AWS**. The app is a simple **Book Register** that allows you to add, view, and delete book records.

## Overview

**MEAN Stack Components**

1. **MongoDB** – Stores and retrieves data
2. **Express** – Handles API requests and routes
3. **AngularJS** – Manages the front-end
4. **Node.js** – Runs the server and connects all components

## Prerequisites

* AWS account with an **Ubuntu EC2 instance**
* Basic knowledge of **Linux commands**, **Node.js**, and **MongoDB**

## Project Setup

1. **Update system and install dependencies**

   ```bash
   sudo apt update && sudo apt upgrade -y
   sudo apt install -y nodejs npm mongodb-org
   ```

2. **Start MongoDB**

   ```bash
   sudo systemctl start mongod
   sudo systemctl enable mongod
   ```

3. **Create the project structure**

   ```bash
   mkdir Books && cd Books
   npm init -y
   ```

4. **Install required Node.js packages**

   ```bash
   npm install express mongoose body-parser
   ```

5. **Start the server**

   ```bash
   node server.js
   ```

   The server runs on **port 3300**.

## Accessing the App

* Locally: `http://localhost:3300`
* Remotely: `http://<EC2-Public-IP>:3300`
  (Ensure port 3300 is open in your AWS security group)

## Project Structure

```
Books/
├── apps/
│   ├── models/
│   │   └── book.js
│   └── routes.js
├── public/
│   ├── index.html
│   └── scripts.js
├── server.js
└── package.json
```

## Features

* Add new books
* View all books
* Delete books by ISBN

## Author

Deployment and documentation by **[Your Name]**
Project for **MEAN Stack Learning and Practice**

---

Would you like me to make this version include the actual `curl` test and browser access example (like the screenshots you described)?
