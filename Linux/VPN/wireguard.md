# WireGuard VPN Server Installation on Ubuntu VM

WireGuard is a modern, fast, and secure VPN solution that is easy to configure and use. In this guide, we will install and configure a WireGuard VPN server on an Ubuntu VM to provide secure remote access to your network.

## 1. Installing WireGuard

First, we need to install the WireGuard package on the VM.

### 1.1 Update the Ubuntu package manager

Update the package manager to ensure that all available packages are up to date.

```bash
sudo apt update
sudo apt upgrade
```

### 1.2 Install WireGuard

Install the WireGuard package using the following command:

```bash
sudo apt install wireguard -y
```

> **Note:** The `-y` flag automatically confirms the installation, so you don't need to manually type `yes` during the process.

## 2. Generating Keys

Generate the private and public keys for the WireGuard server.

### 2.1 Generate the server's private and public keys

Navigate to the `/etc/wireguard` directory and generate the keys:

```bash
cd /etc/wireguard
wg genkey | tee server_private.key | wg pubkey > server_public.key
```

> **Note:** Save the keys securely and do not share them with anyone. The `server_private.key` file contains the private key, and the `server_public.key` file contains the public key.

### 2.2 Generate the client's private and public keys

On the client machine, generate the private and public keys for the client:

```bash
umask 077
wg genkey | tee client_private.key | wg pubkey > client_public.key
```

> **Note:** Save the keys securely and do not share them with anyone. The `client_private.key` file contains the private key, and the `client_public.key` file contains the public key.

## 3. Creating the WireGuard Configuration File

Create and edit the WireGuard configuration file on the server.

### 3.1 Create the WireGuard configuration file

```bash
sudo nano /etc/wireguard/wg0.conf
```

Add the following configuration to the file:

```bash
[Interface]
PrivateKey = <server_private_key>
Address = 10.0.0.1/24
ListenPort = 51820

PostUp = iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o <your_network_interface> -j MASQUERADE
PostDown = iptables -t nat -D POSTROUTING -s 10.0.0.0/24 -o <your_network_interface> -j MASQUERADE

[Peer]
PublicKey = <client_public_key>
AllowedIPs = 10.0.0.2/32
```


> **Note:**  
> * Replace `<server_private_key>` with the content of `server_private.key`.
> * Replace `<your_network_interface>` with your network interface (e.g., `eth0`, `ens33`).
> * Replace `<client_public_key>` with the content of the client's `client_public.key` file.
> * The `10.0.0.0/24` IP range used in this example is just a placeholder. You can use any IP range that does not conflict with your existing network. Ensure that the IP range you choose does not overlap with your local network's IP range.

## 4. Enabling IPv4 Forwarding

Enable IPv4 forwarding in the system configuration.

### 4.1 Edit the sysctl configuration file

```bash
sudo nano /etc/sysctl.conf
```

Add or uncomment the following line:

```bash
net.ipv4.ip_forward=1
```

> **Note:** This setting allows the server to forward IPv4 packets, which is necessary for NAT (Network Address Translation).

### 4.2 Apply the changes

```bash
sudo sysctl -p
```

> **Note:** This command reloads the sysctl configuration to apply the changes immediately.

## 5. Starting and Enabling the WireGuard Service

Now that WireGuard is configured, you need to start the service and enable it to start on boot.

```bash
sudo systemctl start wg-quick@wg0
sudo systemctl enable wg-quick@wg0
```

### 5.1 Verify the WireGuard service status

```bash
sudo wg
```

> **Note:** This command displays the current status of the WireGuard interface, including connected peers and their statuses.

## 6. Client Configuration

To connect a client to the WireGuard VPN server, you need to configure the client with the appropriate settings

### 6.1 Create the client configuration file

On the client device, create a new configuration file named:

```bash
wg0.conf
```

Add the following configuration to the file:

```bash
[Interface]
PrivateKey = <client_private_key>
Address = 10.0.0.2/32
DNS = 1.1.1.1

[Peer]
PublicKey = <server_public_key>
Endpoint = <server_public_ip_or_domain_name>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
```

> **Note:**
> * Replace `<client_private_key>` with the content of the client's `client_private.key` file.
> * Replace `<server_public_key>` with the content of the server's `server_public.key` file.
> * Replace `<server_public_ip_or_domain_name>` with the public IP address or the domain name of the WireGuard server.
> * The `PersistentKeepalive` setting ensures that the connection remains active even if there is no data being sent.
> * Ensure that the `Address` field in the client configuration matches the IP address specified in the `AllowedIPs` field of the server configuration.

### 6.2 Using WireGuard on Mobile Devices

For mobile devices, you can use the WireGuard app available on both Android and iOS platforms.

1. Install the WireGuard app from the Google Play Store (Android) or App Store (iOS).
2. Open the app and tap on the "+" icon to add a new tunnel.
3. Import the configuration file (`wg0.conf`) by scanning the QR code or manually entering the configuration details.
4. Activate the tunnel to establish the VPN connection.

### 6.3 Using WireGuard on Windows

For Windows devices, you can use the WireGuard app available from the official WireGuard website.

1. Download and install the WireGuard app from the official website.
2. Open the app and click on "Import tunnel(s) from file".
3. Select the configuration file (`wg0.conf`) and import it.
4. Activate the tunnel to establish the VPN connection.
