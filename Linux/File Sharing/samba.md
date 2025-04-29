# Samba File Sharing Setup on Ubuntu VM
Samba allows your Ubuntu server to share files with Windows and Linux clients across a local network. This guide walks you through installing and configuring Samba to create a shared folder accessible from both Windows and Linux.

## 1. Installing Samba
Before you can use Samba, you need to install the package on your Ubuntu VM.
### 1.1 Update the Ubuntu Package Manager
First, ensure your system is fully up to date to avoid compatibility issues:
```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install the Samba Package
Install Samba and its required utilities:
```bash
sudo apt install samba samba-common-bin
```
> **Note**: The `samba-common-bin` package includes additional tools like `testparm` and `smbpasswd`, useful for testing and managing users.

## 2. Create the Shared Directory
Next, you'll create the folder that will be shared with clients over the network.
```bash
sudo mkdir -p /srv/samba/shared
```
Set the appropriate permissions so the Samba service can access and manage files:
```bash
sudo chown your_user:your_user /srv/samba/shared
sudo chmod 0775 /srv/samba/shared
```

## 3. Configuring Samba
Samba's configuration file is located at `/etc/samba/smb.conf`.
### 3.1 Open the Configuration File
```bash
sudo nano /etc/samba/smb.conf
```

### 3.2 Add a Share Definition
Scroll to the bottom and add the following:
```bash
[Shared]
   path = /srv/samba/shared
   browseable = yes
   read only = no
   guest ok = yes
   force user = nobody
   create mask = 0660
   directory mask = 0771
```
> **Explanation:**
> * `guest ok = yes`: Allows unauthenticated users to access the share.
> * `force user = nobody`: Ensures all files are accessed/created as the 'nobody' user.
> * `create mask` and `directory mask`: Control file and folder permission modes.

### 3.3 Test the Configuration
Check for syntax errors:
```bash
testparm
```
If the output ends with `Loaded services file OK`, you're good to go.

## 4. Enable and Restart the Samba Service
Apply your configuration and start the Samba daemon:
```bash
sudo systemctl restart smbd
sudo systemctl enable smbd
```

## 5. Optional: Set Up User Authentication
For secure access with credentials instead of guest login:

### 5.1 Create a System User
If not already created:ű
```bash
sudo adduser sambauser
```

### 5.2 Set a Samba Password for the User
```bash
sudo smbpasswd -a sambauser
```
You will be prompted to enter a Samba password.

### 5.3 Modify the Share Block
Update the `[Shared]` section in `/etc/samba/smb.conf`:
```bash
[Shared]
   path = /srv/samba/shared
   valid users = sambauser
   guest ok = no
   read only = no
   create mask = 0660
   directory mask = 0771
```
Then restart Samba:
```bash
sudo systemctl restart smbd
```