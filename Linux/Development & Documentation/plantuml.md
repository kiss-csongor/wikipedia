# PlantUML Server Installation on Ubuntu with Docker

PlantUML is a powerful tool that allows you to generate UML diagrams from plain text descriptions. In this guide, we will set up a PlantUML server using Docker and expose it securely via Nginx with HTTPS support.

## 1. Installing Required Packages

First, we need to install Docker, Docker Compose, Nginx, and Certbot on the Ubuntu system.

```bash
sudo apt update
sudo apt install docker.io docker-compose nginx -y
sudo systemctl enable docker
sudo apt install certbot python3-certbot-nginx
```

> **Note:** Docker will be used to run the PlantUML server, and Nginx will serve as a reverse proxy with HTTPS support via Let's Encrypt.

## 2. Creating the Initial Nginx Configuration

Set up an HTTP server block for your domain.

### 2.1 Create the Nginx configuration file

```bash
sudo nano /etc/nginx/sites-available/plantuml
```

```bash
server {
    listen 80;
    server_name <your-domain>;

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

> **Note:** Replace `<your-domain>` with your actual domain name.

### 2.2 Enable the site and reload Nginx

```bash
sudo ln -s /etc/nginx/sites-available/plantuml /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## 3. Obtain an SSL Certificate

Use Certbot to obtain an SSL certificate for your domain.

```bash
sudo certbot --nginx -d <your-domain>
```

> **Note:** Replace `<your-domain>` with your actual domain name.

## 4. Update the Nginx Configuration for HTTPS

Replace the contents of the `plantuml` file with the secure version:

```bash
sudo nano /etc/nginx/sites-available/plantuml
```

```bash
server {
    listen 80;
    server_name <your-domain>;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name <your-domain>;

    ssl_certificate /etc/letsencrypt/live/<your-domain>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<your-domain>/privkey.pem;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_pass http://plantuml:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

> **Note:** Replace `<your-domain>` with your actual domain name.

## 5. Create the Dockerfile for Nginx

In your deployment directory (e.g., `/home/kisscsongor/plantuml-server/`), create a file named `Dockerfile-nginx`:

```bash
FROM nginx:alpine

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
```

## 6. Create the Docker Compose File

In the same directory, create the `docker-compose.yml` file:

```bash
version: '3'

services:
  plantuml:
    image: plantuml/plantuml-server
    container_name: plantuml-server
    ports:
      - "8080:8080"
    restart: unless-stopped

  nginx:
    build:
      context: .
      dockerfile: Dockerfile-nginx
    ports:
      - "80:80"
      - "443:443"
    depends_on:
      - plantuml
    volumes:
      - /etc/nginx/sites-available/plantuml:/etc/nginx/conf.d/default.conf
      - /etc/letsencrypt/live/<your-domain>/fullchain.pem:/etc/letsencrypt/live/<your-domain>/fullchain.pem
      - /etc/letsencrypt/live/<your-domain>/privkey.pem:/etc/letsencrypt/live/<your-domain>/privkey.pem
```

> **Note:** Make sure to replace `<your-domain>` with your actual domain in all paths.

## 7. Create a Systemd Service for Docker Compose

Create a systemd service to ensure your containers run on system startup.

```bash
sudo nano /etc/systemd/system/plantuml.service
```

Add the following content:

```bash
[Unit]
Description=Docker for PlantUML
After=docker.service
Requires=docker.service

[Service]
WorkingDirectory=/home/kisscsongor/
ExecStart=/usr/bin/docker-compose up --build
ExecStop=/usr/bin/docker-compose down --remove-orphans --volumes
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

> **Note:** Make sure the `WorkingDirectory` matches the location of your `docker-compose.yml` file.

## 8. Enable and Start the PlantUML Service

Use the systemctl command to start and enable the service:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable plantuml
sudo systemctl start plantuml
```

> **Note:** Your PlantUML server should now be running and accessible via `https://<your-domain>`.