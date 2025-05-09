# PostgreSQL Installation and Basic Configuration on Ubuntu VM
PostgreSQL is a powerful, open-source relational database management system. In this guide, we will install and configure PostgreSQL on an Ubuntu VM.

## 1. Installing PostgreSQL
### 1.1 Update the Ubuntu package manager
Update the package manager to ensure that all available packages are up to date.
```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install PostgreSQL
Install the PostgreSQL package using the following command:
```bash
sudo apt install postgresql postgresql-contrib
```
This command installs the PostgreSQL server and the additional utilities and extensions provided by the `postgresql-contrib` package.

### 1.3 Check the PostgreSQL service status
After the installation, the PostgreSQL service should be started automatically. You can check its status using the following command:
```bash
sudo systemctl status postgresql
```

## 2. Configuring PostgreSQL
### 2.1 Access the PostgreSQL prompt
PostgreSQL creates a default user named `postgres`. You can access the PostgreSQL prompt using the following command:
```bash
sudo -u postgres psql
```

### 2.2 Set a password for the postgres user
While in the PostgreSQL prompt, set a password for the `postgres` user:
```bash
ALTER USER postgres PASSWORD '<your_password>';
```
> **Note**: Replace `<your_password>` with a strong password of your choice.

### 2.3 Create a new database user
You can create a new database user with the following command:
```bash
CREATE USER <your_username> WITH PASSWORD '<your_password>';
```
> **Note**: Replace `<your_username>` and `<your_password>` with the desired username and password.

### 2.4 Create a new database
Create a new database and grant the necessary privileges to the user:
```bash
CREATE DATABASE <your_database>;
GRANT ALL PRIVILEGES ON DATABASE <your_database> TO <your_username>;
```
> **Note**: Replace `<your_database>` and `<your_username>` with the desired database name and username.

### Optional: 2.5 Configure remote access
If you want to allow remote connections to your PostgreSQL server, you need to modify the `postgresql.conf` and `pg_hba.conf` files.

1. Open the `postgresql.conf` file:
```bash
sudo nano /etc/postgresql/<version>/main/postgresql.conf
```
> **Note**: Replace `<version>` with the installed PostgreSQL version (e.g., `14`).

2. Find the line containing `listen_addresses` and change it to:
```bash
listen_addresses = '*'
```

3. Open the `pg_hba.conf` file:
```bash
sudo nano /etc/postgresql/<version>/main/pg_hba.conf
```
> **Note**: Replace `<version>` with the installed PostgreSQL version (e.g., `14`).

4. Add the following line to allow remote connections from a specific IP address:
```bash
host    all             all             <ip_address>/32            md5
```
> **Note**: Replace <ip_address> with the IP address of the client machine.

5. Restart the PostgreSQL service to apply the changes:
```bash
sudo systemctl restart postgresql
```

# 3. Connecting to the PostgreSQL database
You can connect to the PostgreSQL database using the `psql` command-line tool or a graphical client like pgAdmin.
## 3.1 Using the psql command-line tool
Connect to the database using the following command:
```bash
psql -U <your_username> -d <your_database> -h <ip_address> -W
```
> **Note**: Replace `<your_username>`, `<your_database>`, and `<ip_address>` with the appropriate values.