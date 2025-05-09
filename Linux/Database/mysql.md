# MySQL Installation and Basic Configuration on Ubuntu VM
MySQL is a widely used open-source relational database management system. In this guide, we will install and configure MySQL on an Ubuntu VM.

## 1. Installing MySQL
### 1.1 Update the Ubuntu package manager
Update the package manager to ensure that all available packages are up to date.
```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install MySQL
Install the MySQL package using the following command:
```bash
sudo apt install mysql-server
```
This command installs the MySQL server and the necessary dependencies.

### 1.3 Secure the MySQL installation
After the installation, you should run the security script that comes with MySQL. This script helps improve the security of your MySQL installation by setting the root password, removing anonymous users, disallowing remote root login, and removing test databases.
```bash
sudo mysql_secure_installation
```
Follow the on-screen instructions to set the root password and configure the security options.

### 1.4 Check the MySQL service status
After the installation, the MySQL service should be started automatically. You can check its status using the following command:
```bash
sudo systemctl status mysql
```

# 2. Configuring MySQL
### 2.1 Access the MySQL prompt
You can access the MySQL prompt using the following command:
```bash
sudo mysql -u root -p
```
You will be prompted to enter the root password that you set during the `mysql_secure_installation` process.

### 2.2 Create a new database user
You can create a new database user with the following command:
```bash
CREATE USER '<your_username>'@'localhost' IDENTIFIED BY '<your_password>';
```
> **Note**: Replace `<your_username>` and `<your_password>` with the desired username and password.

## 2.3 Create a new database
Create a new database and grant the necessary privileges to the user:
```bash
CREATE DATABASE <your_database>;
GRANT ALL PRIVILEGES ON <your_database>.* TO '<your_username>'@'localhost';
FLUSH PRIVILEGES;
```
> **Note**: Replace `<your_database>` and `<your_username>` with the desired database name and username.

## Optional: 2.4 Configure remote access
If you want to allow remote connections to your MySQL server, you need to modify the MySQL configuration file and grant remote access to the user.
1. Open the MySQL configuration file:
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

2. Find the line containing `bind-address` and change it to:
```bash
bind-address = 0.0.0.0
```

3. Save the file and exit the editor.
4. Restart the MySQL service to apply the changes:
```bash
sudo systemctl restart mysql
```

5. Grant remote access to the user:
```bash
GRANT ALL PRIVILEGES ON <your_database>.* TO '<your_username>'@'%' IDENTIFIED BY '<your_password>';
FLUSH PRIVILEGES;
```
> **Note**: Replace `<your_database>`, `<your_username>`, and `<your_password>` with the appropriate values.

# 3. Connecting to the MySQL database
You can connect to the MySQL database using the `mysql` command-line tool or a graphical client like MySQL Workbench.
## 3.1 Using the `mysql` command-line tool
Connect to the database using the following command:
```bash
mysql -u <your_username> -p -h <ip_address> -D <your_database>
```
> **Note**: Replace `<your_username>`, `<your_database>`, and `<ip_address>` with the appropriate values.



