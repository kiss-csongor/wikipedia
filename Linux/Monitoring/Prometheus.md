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

4. Create a symbolic link for easier access:

```bash
sudo tar -xvzf prometheus-2.45.0.linux-amd64.tar.gz
```

### 1.3 Configuring Prometheus

Create a configuration file for Prometheus that defines where it should collect data from (e.g., system metrics, node metrics).

1. Create a new configuration file (prometheus.yml) with the following content:

```bash
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']
```

> Note: This configuration sets the scrape_interval to 15 seconds and defines a target job named node that scrapes metrics from `localhost:9100`, which is typically the default port for node_exporter (Prometheus node exporter). 

### 1.4 Configuring Prometheus systemd service

Create a systemd file for Prometheus service (prometheus.service) with the following content:

```bash
[Unit]
Description=Prometheus Monitoring System
Documentation=https://prometheus.io/docs/introduction/overview/
After=network.target

[Service]
ExecStart=/opt/prometheus/prometheus --config.file=/opt/prometheus/prometheus.yml
ExecStop=/bin/kill -TERM $MAINPID
User=root
Group=root
Restart=always
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

### 1.5 Start the Prometheus service

```bash
sudo systemctl enable prometheus.service
sudo systemctl start prometheus.service
```