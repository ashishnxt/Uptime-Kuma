# **Uptime Kuma**
## **Overview**
Uptime Kuma is a free, open-source, self-hosted monitoring tool that helps track the uptime status of websites, applications, and network services. It offers real-time alerts via various notification methods when monitored services go down.

---

## **Key Features**
- **Comprehensive Monitoring**: Supports HTTP(s), TCP, Ping, DNS, Docker container health checks, and more.
- **User-Friendly Interface**: Intuitive UI/UX for easy configuration and monitoring.
- **Flexible Notification System**: Integrates with over 90 notification services (Telegram, Slack, Discord, Email, Webhooks).
- **Customizable Status Pages**: Allows multiple status pages with custom domains.
- **Advanced Features**: SSL certificate monitoring, proxy support, two-factor authentication (2FA).
---

## **Installation on Docker**
### **Prerequisites**
- Install [﻿Docker](https://docs.docker.com/get-docker/)  and [﻿Docker Compose](https://docs.docker.com/compose/install/) .
- Ensure required ports (default: 3001) are open for access.
---

## **Installing Uptime Kuma on Docker**
### **Version 1 (Stable Release)**
**Docker Run Method:**

```bash
docker run -d --restart=always -p 3001:3001 -v uptime-kuma:/app/data --name uptime-kuma louislam/uptime-kuma:1
```
**Docker Compose (docker-compose.yml)**

```yaml
version: '3.8'
services:
  uptime-kuma:
    image: louislam/uptime-kuma:1
    container_name: uptime-kuma
    restart: always
    ports:
      - "3001:3001"
    volumes:
      - ./data:/app/data
    environment:
      - TZ=UTC  # Set your preferred timezone
```
**Deployment Steps:**

1. Create a directory and navigate to it: mkdir uptime-kuma && cd uptime-kuma
2. Save the above `docker-compose.yml`  file inside the directory.
3. Deploy Uptime Kuma using: docker-compose up -d
4. Open your browser and access `http://localhost:3001` .
---

## **Version 2 (Beta) - Using a Separate MariaDB Database**
### **Step 1: Set Up MariaDB Container**
**Docker Run Method for MariaDB:**

```bash
docker run -d --name kuma-db \
-e MYSQL_ROOT_PASSWORD=rootpassword \
-e MYSQL_DATABASE=uptimekuma \
-e MYSQL_USER=kumauser \
-e MYSQL_PASSWORD=kumapassword \
-v kuma-db:/var/lib/mysql \
--restart=always \
mariadb:latest
```
**Docker Compose (docker-compose.yml) for MariaDB and Uptime Kuma:**

```yaml
version: '3.8'
services:
  mariadb:
    image: mariadb:latest
    container_name: kuma-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: uptimekuma
      MYSQL_USER: kumauser
      MYSQL_PASSWORD: kumapassword
    volumes:
      - kuma-db:/var/lib/mysql
    networks:
      - kuma-network

  uptime-kuma:
    image: louislam/uptime-kuma:beta
    container_name: uptime-kuma-v2
    restart: always
    ports:
      - "3002:3001"
    volumes:
      - ./data:/app/data
    environment:
      - TZ=UTC
      - DATABASE_TYPE=mysql
      - DATABASE_HOST=kuma-db
      - DATABASE_PORT=3306
      - DATABASE_NAME=uptimekuma
      - DATABASE_USER=kumauser
      - DATABASE_PASSWORD=kumapassword
    depends_on:
      - mariadb
    networks:
      - kuma-network

volumes:
  kuma-db:

networks:
  kuma-network:
```
### **Step 2: Deploy the Setup**
1. Create a directory and navigate to it: mkdir uptime-kuma-v2 && cd uptime-kuma-v2
2. Save the above `docker-compose.yml`  file inside the directory.
3. Start the containers: docker-compose up -d
4. Open your browser and go to `http://localhost:3002` .
---

## **Usage and Monitoring Setup**
### **Adding a New Monitor**
1. Open the Uptime Kuma dashboard.
2. Click **"Add New Monitor"**.
3. Choose the **monitoring type** (HTTP(s), Ping, TCP, DNS, etc.).
4. Enter details such as URL, monitoring interval, and expected response.
5. Configure notification settings.
6. Click **"Save"** to start monitoring.
### **Monitor Types Available**
- **HTTP(s)**: Check website availability.
- **Ping**: Test server connectivity.
- **TCP Port**: Monitor a specific port on a server.
- **DNS Record**: Verify DNS resolution.
- **Docker Container**: Track container health.
### **Configuring Notifications**
Go to **Settings > Notification**, choose a method (Email, Telegram, Slack, Webhooks), and enter the required credentials.

---

## **Benefits of Using Uptime Kuma**
- **Free & Open Source**: No licensing fees.
- **Self-Hosted**: Full control over your data.
- **Multi-Protocol Monitoring**: HTTP(s), TCP, DNS, Ping, and more.
- **Custom Alerts**: Flexible notification options.
- **Status Page Creation**: Share uptime status with users.
- **Lightweight & Efficient**: Minimal resource usage.
---

## **Best Practices for System Monitoring**
- **Use Multiple Monitoring Methods**: HTTP, Ping, TCP for redundancy.
- **Enable Notifications**: Receive alerts for service downtime.
- **Monitor SSL Expiry**: Avoid service disruptions.
- **Optimize Check Intervals**: Set reasonable polling intervals (e.g., 60 seconds).
- **Deploy Uptime Kuma on a Separate Server**: Prevent self-monitoring issues.


 

