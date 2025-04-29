# Apache Web Server Installation on Ubuntu VM

Apache Web Server is one of the most widely used open-source web servers, known for its flexibility, stability, and powerful feature set. This guide explains how to install and configure Apache on an Ubuntu VM to serve web content.

## 1. Installing Apache

Begin by installing the Apache package on your Ubuntu virtual machine.

### 1.1 Update the Ubuntu package manager

Ensure your package list is up to date:

```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install Apache

Install Apache using the following command:

```bash
sudo apt install apache2
```

## 2. 2. Configuring Apache

Apache configuration files are located in the `/etc/apache2/` directory. The main configuration file is `apache2.conf`.

### 2.1 Check Apache default configuration

Apache typically works out-of-the-box after installation. You can view or edit the global configuration with:

```bash
sudo nano /etc/apache2/apache2.conf
```
Most settings can remain as default unless you have specific needs.

### 2.2 Create a Virtual Host Configuration

Apache uses "virtual hosts" similar to NGINX's "server blocks" to manage multiple sites.

Create a new configuration file:

```bash
sudo nano /etc/apache2/sites-available/your_domain.conf
```

Add the following configuration:

```bash
<VirtualHost *:80>
    ServerAdmin webmaster@your_domain
    ServerName your_domain
    ServerAlias www.your_domain
    DocumentRoot /var/www/your_domain
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

> **Note:** Replace `your_domain` with your actual domain name (e.g., `example.com`).

### 2.3 Enable the Virtual Host

Enable your newly created virtual host:

```bash
sudo a2ensite your_domain.conf
```

Disable the default site if not needed:

```bash
sudo a2dissite 000-default.conf
```

Reload Apache to apply the changes:

```bash
sudo systemctl reload apache2
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

Create a sample HTML file to test the Apache configuration.

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
    <p>This is a sample page served by Apache.</p>
</body>
</html>
```

## 5. Test Apache Configuration

Check for syntax errors in the configuration:

```bash
sudo apache2ctl configtest
```

> **Note:** You should see: `Syntax OK`.

## 6. Restart Apache

Restart Apache to apply all changes:

```bash
sudo systemctl restart apache2
```

## 7. Verify the Apache Service Status

Ensure Apache is running correctly:

```bash
sudo systemctl status apache2
```

## 8. Optional: Configure SSL with Let's Encrypt

You can secure your site with a free SSL certificate from Let’s Encrypt.

### 8.1 Install Certbot

Install Certbot and the Apache plugin:

```bash
sudo apt install certbot python3-certbot-apache
```

### 8.2 Obtain an SSL Certificate

Run Certbot to automatically configure Apache and install the SSL certificate:

```bash
sudo certbot --apache -d your_domain -d www.your_domain
```

Follow the prompts to complete the setup.

> **Note:** Replace `your_domain` with your actual domain name (e.g.,` example.com`).

### 8.3. Verify SSL Configuration

After obtaining the SSL certificate, Certbot will automatically configure Apache to use SSL. You can verify the configuration by accessing your website using `https://your_domain`.

### 8.4. Renew SSL Certificates Automatically

Certbot automatically installs a cron job for certificate renewal. Test it manually with:

```bash
sudo certbot renew --dry-run
```

> **Important**: For certificate renewal to succeed, **port 80 (HTTP)** must be open and accessible from the internet, as Let's Encrypt uses this port to verify domain ownership. Ensure that your firewall (e.g., UFW, security group, or cloud provider firewall) allows incoming traffic on **port 80**.

## 9. Optional: Apache Configuration After Enabling HTTPS

After obtaining an SSL certificate with Certbot, you may want to manually fine-tune your Apache configuration for HTTPS. This includes setting up redirection from **HTTP (port 80)** to **HTTPS (port 443)** and defining a secure VirtualHost for SSL.

### 9.1 Redirect HTTP to HTTPS

Edit your HTTP virtual host file to redirect traffic to HTTPS:

```bash
sudo nano /etc/apache2/sites-available/your_domain.conf
```

Modify the <VirtualHost *:80> block as follows:

```bash
<VirtualHost *:80>
    ServerName your_domain
    ServerAlias www.your_domain
    Redirect permanent / https://your_domain/
</VirtualHost>
```

> **Note**: This ensures that any requests over HTTP will be redirected to HTTPS.

## 9.2 Create an HTTPS Virtual Host

Create a new configuration file or modify the one Certbot generated for HTTPS:

```bash
sudo nano /etc/apache2/sites-available/your_domain-le-ssl.conf
```

Use the following content (Certbot may generate this automatically, but if not, you can set it up manually):

```bash
<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerAdmin webmaster@your_domain
    ServerName your_domain
    ServerAlias www.your_domain

    DocumentRoot /var/www/your_domain

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/your_domain/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/your_domain/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
</IfModule>
```

> **Important**: Replace `your_domain` with your actual domain name and ensure the certificate paths match those issued by Let's Encrypt.

## 9.3 Enable SSL and Rewrite Modules

Make sure the required Apache modules are enabled:

```bash
sudo a2enmod ssl
sudo a2enmod rewrite
```

Enable the SSL site configuration (if not already enabled):

```bash
sudo a2ensite your_domain-le-ssl.conf
```

## 9.4 Restart Apache

After saving your configuration changes, restart Apache to apply them:

```bash
sudo apache2ctl configtest
sudo systemctl reload apache2
```

You should now be able to access your site securely via `https://your_domain`, and any HTTP request should redirect automatically to HTTPS.

