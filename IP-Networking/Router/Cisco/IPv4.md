# Cisco IPv4 Address Configuration

## 1. Enter Global Configuration Mode
```bash
enable
configure terminal
```

## 2. Assign an IPv4 Address to an Interface
```bash
interface gigabitethernet0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
```

## 3. Activate the Interface
```bash
no shutdown
```

## 4. Verify the Configuration
```bash
show ip interface brief
```