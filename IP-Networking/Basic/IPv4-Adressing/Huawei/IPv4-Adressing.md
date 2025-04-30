# Huawei IPv4 Address Configuration

## 1. Enter System View
```bash
system-view
```

## 2. Assign an IPv4 Address to an Interface
```bash
interface GigabitEthernet0/0/0
ip address 192.168.1.1 255.255.255.0
```

## 3. Activate the Interface
```bash
undo shutdown
```

## 4. Verify the Configuration
```bash
display ip interface brief
```