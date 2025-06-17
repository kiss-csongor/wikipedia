# Docker and Docker Compose Installation on Ubuntu VM
Docker is a popular platform for developing, shipping, and running applications in containers. This guide walks you through installing Docker and Docker Compose without using Snap on an Ubuntu VM.

## 1. Installing Docker
### 1.1 Update the Ubuntu package manager
Ensure all your packages are up to date before installing Docker
```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install required dependencies
Install packages required to use Docker’s official repository:
```bash
sudo apt install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

### 1.3 Add Docker’s official GPG key
Create a directory for the key if it doesn’t exist and add Docker's GPG key:
```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
    sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

### 1.4 Set up the Docker repository
Add the Docker APT repository to your system:
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 1.5 Install Docker Engine
Update the package index again and install Docker:
```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 1.6 Verify Docker installation
Check Docker version and status:
```bash
docker --version
sudo systemctl status docker
```

### 1.7 Enable and start Docker on boot
```bash
sudo systemctl enable docker
sudo systemctl start docker
```
