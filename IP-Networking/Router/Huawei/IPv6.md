# Huawei IPv6 Address Configuration

## 1. Enter System View
```bash
system-view
```

## 2. Enable IPv6 (if required)
```bash
ipv6
```

## 3. Assign an IPv6 Address to an Interface
```bash
interface GigabitEthernet0/0/0
ipv6 address 2001:db8:1::1 64
```

## 4. Activate the Interface
```bash
undo shutdown
```

## 5. Verify the Configuration
```bash
display ipv6 interface brief
```