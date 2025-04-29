# Cisco IPv6 Address Configuration

## 1. Enable IPv6 Routing Globally
```bash
configure terminal
ipv6 unicast-routing
```

## 2. Assign an IPv6 Address to an Interface
```bash
interface gigabitethernet0/0
ipv6 address 2001:db8:1::1/64
```

## 3. Activate the Interface
```bash
no shutdown
```

## 4. Verify the Configuration
```bash
show ipv6 interface brief
```