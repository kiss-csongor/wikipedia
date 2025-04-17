# Prometheus and Grafana Installation on Ubuntu VM

Prometheus is a powerful open-source monitoring and alerting toolkit that collects and stores metrics in a time-series database. In this guide, we will install Prometheus on an Ubuntu VM to collect data about the system's resources.

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
      - targets: ['<your.domain>:9100']
```

> **Note:** This configuration sets the scrape_interval to 15 seconds and defines a target job named node that scrapes metrics from `<your.domain>:9100`, which is typically the default port for node_exporter (Prometheus node exporter).

### 1.4 Configuring Prometheus systemd service

To enable Prometheus to start automatically at boot, you need to create a systemd service file for Prometheus. This service will manage the Prometheus process.

1. Create the `prometheus.service` file under `/etc/systemd/system/`:

```bash
sudo nano /etc/systemd/system/prometheus.service
```

2. Add the following content to the file:

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

### 1.5 Installing Node Exporter

The Node Exporter is a Prometheus exporter that allows Prometheus to collect system-related metrics like CPU, memory, disk usage, and other system statistics.

1. Navigate to the `/opt` directory:

```bash
cd /opt
```

2. Download the Node Exporter binary:

```bash
sudo wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
```

3. Extract the downloaded tar file:

```bash
sudo tar -xvzf node_exporter-1.3.1.linux-amd64.tar.gz
```

4. Create a symbolic link for easier access:

```bash
sudo ln -s node_exporter-1.3.1.linux-amd64 node_exporter
```

### 1.4 Configuring Node Exporter systemd service

To manage Node Exporter as a service, you also need to create a systemd service file for it.

1. Create the `node_exporter.service` file under `/etc/systemd/system/`:

```bash
sudo nano /etc/systemd/system/node_exporter.service
```

2. Add the following content to the file:

```bash
[Unit]
Description=Prometheus Node Exporter
Documentation=https://prometheus.io/docs/exporters/node_exporter/
After=network.target

[Service]
ExecStart=/opt/node_exporter/node_exporter
ExecStop=/bin/kill -TERM $MAINPID
User=root
Restart=always
LimitNOFILE=4096

[Install]
WantedBy=default.target
```

### 1.7 Start the Prometheus & Node exporter service

Now that you have created the systemd service files for both Prometheus and Node Exporter, enable them to start on boot and then start the services.

```bash
sudo systemctl enable prometheus.service && sudo systemctl enable node_exporter.service
sudo systemctl start prometheus.service && sudo systemctl start node_exporter.service
```
