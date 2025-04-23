# NGINX Web Server Installation on Ubuntu VM

NGINX is a popular open-source web server known for its high performance, stability, and low resource consumption. In this guide, we will install and configure NGINX on an Ubuntu VM to serve web content.

## 1. Installing NGINX

First, we need to install the NGINX package on the VM, which will handle web server tasks.

### 1.1 Update the Ubuntu package manager

Update the package manager to ensure that all available packages are up to date.

```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install NGINX

Install the NGINX package using the following command:

```bash
sudo apt install nginx
```

## 2. Configuring NGINX

NGINX is configured using several configuration files located in the `/etc/nginx/` directory. The main configuration file is `nginx.conf`.

### 2.1 Edit the `nginx.conf` file

Open the `nginx.conf` file to configure the global settings for NGINX.

```bash
sudo nano /etc/nginx/nginx.conf
```

Ensure the following basic settings are configured:

```bash
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
    # multi_accept on;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

### 2.2 Create a Server Block

Create a new server block configuration file for your website.

```bash
sudo nano /etc/nginx/sites-available/<your_domain>
```

Add the following content to the file:

```bash
server {
    listen 80;
    server_name your_domain www.your_domain;

    root /var/www/your_domain;
    index index.html index.htm index.nginx-debian.html;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;
    location = /404.html {
        internal;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        internal;
    }
}
```

> **Note:** Replace `your_domain` with your actual domain name (e.g., `example.com`).

### 2.3 Enable the Server Block

Create a symbolic link to enable the server block.

```bash
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```

## 3. Create the Document Root Directory

Create the directory to store your website files.

```bash
sudo mkdir -p /var/www/your_domain
```

Set the appropriate permissions for the directory.

```bash
sudo chown -R www-data:www-data /var/www/your_domain
sudo chmod -R 755 /var/www/your_domain
```

## 4. Create a Sample HTML File

Create a sample HTML file to test the NGINX configuration.

```bash
sudo nano /var/www/your_domain/index.html
```

Add the following content to the file:

```bash
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to Your Domain!</title>
</head>
<body>
    <h1>Welcome to Your Domain!</h1>
    <p>This is a sample page served by NGINX.</p>
</body>
</html>
```

## 5. Test the NGINX Configuration

Test the NGINX configuration to ensure there are no syntax errors.

```bash
sudo nginx -t
```

## 6. Restart NGINX

Restart the NGINX service to apply the changes.

```bash
sudo systemctl restart nginx
```

## 7. Verify the NGINX Service Status

Check the status of the NGINX service to ensure it is running correctly.

```bash
sudo systemctl status nginx
```

## 8. Optional: Configure SSL with Let's Encrypt

To secure your website with SSL, you can use Let's Encrypt to obtain a free SSL certificate.

### 8.1 Install Certbot

Install Certbot and the NGINX plugin.

```bash
sudo apt install certbot python3-certbot-nginx
```

### 8.2 Obtain an SSL Certificate

Run Certbot to obtain and install the SSL certificate.

```bash
sudo certbot --nginx -d your_domain -d www.your_domain
```

Follow the on-screen instructions to complete the process.

> **Note:** Replace `your_domain` with your actual domain name (e.g.,` example.com`).

## 9. Verify SSL Configuration

After obtaining the SSL certificate, Certbot will automatically configure NGINX to use SSL. You can verify the configuration by accessing your website using `https://your_domain`.

## 10. Renew SSL Certificates Automatically

Certbot sets up automatic renewal of SSL certificates. You can test the renewal process with the following command:

```bash
sudo certbot renew --dry-run
```