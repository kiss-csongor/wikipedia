# ISC-DHCP-Server Installation on Ubuntu VM

The ISC-DHCP-Server is a widely used open-source DHCP (Dynamic Host Configuration Protocol) server that provides IP addresses and other network configuration parameters to clients on a network. In this guide, we will install and configure the ISC-DHCP-Server on an Ubuntu VM.

## 1. Installing ISC-DHCP-Server

First, we need to install the ISC-DHCP-Server on the VM, which will manage the IP address allocation for the network.

### 1.1 Update the Ubuntu package manager

Update the package manager to ensure that all available packages are up to date.

```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install ISC-DHCP-Server

Install the ISC-DHCP-Server package using the following command:

```bash
sudo apt install isc-dhcp-server
```

### 1.3 Configuring ISC-DHCP-Server

The ISC-DHCP-Server is configured using the `dhcpd.conf` file located at `/etc/dhcp/dhcpd.conf`. You need to edit this file to set up the DHCP server according to your network requirements.

1. Open the DHCP configuration file:

```bash
sudo nano /etc/dhcp/dhcpd.conf
```

2. Add the following configuration to the file:

```bash
# Define the default lease time for clients (in seconds)
default-lease-time 600;

# Define the maximum lease time for clients (in seconds)
max-lease-time 7200;

# Define the subnet and the range of IP addresses to be allocated
subnet <your.subnet> netmask <your.netmask> {
    # Specify the range of IP addresses that the DHCP server can allocate
    range <start.ip> <end.ip>;

    # Specify the default gateway for the clients
    option routers <your.gateway>;

    # Specify the DNS servers for the clients
    option domain-name-servers <dns1>, <dns2>;

    # Specify the domain name for the clients
    option domain-name "<your.domain>";
}
```
> Note: Replace `<your.subnet>`, `<your.netmask>`, `<start.ip>`, `<end.ip>`, `<your.gateway>`, `<dns1>`, `<dns2>`, and `<your.domain>` with the appropriate values for your network configuration.

### 1.4 Configuring the DHCP server interface

Specify the network interface that the DHCP server should listen on.

1. Open the `isc-dhcp-server` file:

```bash
sudo nano /etc/default/isc-dhcp-server
```

2. Set the `INTERFACESv4` variable to the appropriate network interface (e.g., `eth0`):

```bash
INTERFACESv4="<your.interface>"
```

>Note: Replace `<your.interface>` with the network interface you want the DHCP server to listen on. This is typically the interface connected to the network where DHCP clients are located.

### 1.5 Start the ISC-DHCP-Server service

Now that the ISC-DHCP-Server is configured, you need to start the service and enable it to start on boot.

```bash
sudo systemctl enable isc-dhcp-server
sudo systemctl start isc-dhcp-server
```