# BIND9 DNS Server Installation on Ubuntu VM

BIND9 is a widely used open-source DNS (Domain Name System) server that translates domain names into IP addresses. In this guide, we will install and configure BIND9 on an Ubuntu VM to manage DNS queries for a network.

## 1. Installing BIND9

First, we need to install the BIND9 package on the VM, which will handle DNS queries.

### 1.1 Update the Ubuntu package manager

Update the package manager to ensure that all available packages are up to date.

```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install BIND9

Install the BIND9 package using the following command:

```bash
sudo apt install bind9 bind9utils bind9-doc
```

## 2. Configuring BIND9

BIND9 is configured using several configuration files located in the `/etc/bind/` directory. The main configuration file is `named.conf.local`.

### 2.1 Edit the `named.conf.local` file

Open the `named.conf.local` file to define your DNS zones.

```bash
sudo nano /etc/bind/named.conf.local
```

Add the following configuration to define a forward and reverse lookup zone:

```bash
// Define a forward lookup zone
zone "<your.domain>" {
    type master;
    file "/etc/bind/zones/db.<your.domain>";
};

// Define a reverse lookup zone
zone "<your.reverse.zone>" {
    type master;
    file "/etc/bind/zones/db.<your.reverse.zone>";
};
```

> Note: Replace `<your.domain>` with your actual domain name (e.g., `example.com`) and `<your.reverse.zone>` with your reverse lookup zone (e.g., `1.168.192.in-addr.arpa` for the `192.168.1.0/24` subnet).

### 2.2 Create the zone files

Create the directory to store the zone files:

```bash
sudo mkdir /etc/bind/zones
```

#### 2.2.1 Forward lookup zone file

Create and edit the forward lookup zone file:

```bash
sudo nano /etc/bind/zones/db.<your.domain>
```

Add the following content to the file:

```bash
$TTL    604800
@       IN      SOA     ns1.<your.domain>. admin.<your.domain>. (
                              3         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.<your.domain>.
@       IN      NS      ns2.<your.domain>.

ns1     IN      A       <ns1.ip.address>
ns2     IN      A       <ns2.ip.address>

www     IN      A       <www.ip.address>
```

> Note: Replace `<your.domain>`, `<ns1.ip.address>`, `<ns2.ip.address>`, and `<www.ip.address>` with your actual domain name and IP addresses.

#### 2.2.2 Reverse lookup zone file

Create and edit the reverse lookup zone file:

```bash
sudo nano /etc/bind/zones/db.<your.reverse.zone>
```

Add the following content to the file:

```bash
$TTL    604800
@       IN      SOA     ns1.<your.domain>. admin.<your.domain>. (
                              3         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.<your.domain>.
@       IN      NS      ns2.<your.domain>.

<last.octet>    IN      PTR     <hostname>.<your.domain>.
```

> Note: Replace `<your.domain>`, `<last.octet>`, and `<hostname>` with your actual domain name, the last octet of the IP address, and the hostname.

## 3. Configuring the named.conf.options file

Edit the `named.conf.options` file to configure the DNS server options.

```bash
sudo nano /etc/bind/named.conf.options
```

Add or modify the following lines:

```bash
options {
    directory "/var/cache/bind";

    // Set the DNS server to listen on the appropriate interface
    listen-on { <your.interface.ip>; };

    // Allow queries from the local network
    allow-query { localhost; <your.network>; };

    // Set the forwarders (optional)
    forwarders {
        8.8.8.8;
        8.8.4.4;
        <your.secondary.dns.server.ip>; # (e.g. <your.windows.server.ip>)
    };

    // Set the DNS server to act as a caching server
    recursion yes;
    allow-recursion { localhost; <your.network>; };

    dnssec-validation auto;
    auth-nxdomain no;    # conform to RFC1035
};
```

> Note: Replace `<your.interface.ip>` with the IP address of the network interface you want the DNS server to listen on, and `<your.network>` with your local network range (e.g., `192.168.1.0/24`).

## 4. Start and Enable the BIND9 service

Now that BIND9 is configured, you need to start the service and enable it to start on boot.

```bash
sudo systemctl enable bind9
sudo systemctl start bind9
```

## 5. Verify the BIND9 service status

Check the status of the BIND9 service to ensure it is running correctly.

```bash
sudo systemctl status bind9
```

## 6. Optional: Configure Forward Lookup Zone for Windows Active Directory Integration

To ensure that the BIND9 DNS server advertises the Windows Active Directory Domain Controller for `<your.domain>` domain, modify the forward lookup zone file as follows:

### 6.1 Edit the Forward Lookup Zone File

Open the forward lookup zone file for `<your.domain>`:

```bash
sudo nano /etc/bind/zones/db.<your.domain>
```

Add the following lines to define the Domain Controller and the necessary SRV records:

```bash
; Define the Domain Controller
dc1     IN      A       <dc1.ip.address>

; SRV records for Active Directory
_ldap._tcp.<your.domain>.  IN  SRV  0 100 389 dc1.<your.domain>.
_kerberos._tcp.<your.domain>. IN SRV 0 100 88 dc1.<your.domain>.
_kerberos._udp.<your.domain>. IN SRV 0 100 88 dc1.<your.domain>.
_kpasswd._tcp.<your.domain>. IN SRV 0 100 464 dc1.<your.domain>.
_kpasswd._udp.<your.domain>. IN SRV 0 100 464 dc1.<your.domain>.
```

> Note: Replace `<dc1.ip.address>` with the actual IP address of your Windows Domain Controller and `<your.domain>` with your actual domain name (e.g., `example.com`).
