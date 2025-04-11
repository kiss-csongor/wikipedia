# Prometheus and Grafana Installation on Ubuntu VM

## 1. Installing Prometheus

First, we need to install Prometheus on the VM, which will collect data from the system.

### 1.1 Update the Ubuntu package manager
Update the package manager to ensure that all available packages are up to date.

```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install Prometheus

Prometheus can be installed either through official binaries or ht package manager. Here we'll use the official binaries.

1. Go to the `/opt` directory:

```bash
cd /opt
```

2. Download the Prometheus binary:

```bash
sudo wget https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz

```

3. Extract the downloaded tar file:

```bash
sudo tar -xvzf prometheus-2.45.0.linux-amd64.tar.gz

```

