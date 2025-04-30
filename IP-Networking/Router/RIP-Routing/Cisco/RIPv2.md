# Cisco RIP Configuration (RIPv2 Recommended)


## 1. Enter Global Configuration Mode
```bash
enable
configure terminal
```


## 2. Enable RIP and Specify Version 2
```bash
router rip
version 2
```
> **Note**: RIPv2 supports subnet masks and is classless (RIP v1 is classful and outdated).


## 3. Advertise Connected Networks
```bash
network 192.168.1.0
network 192.168.2.0
```
> **Note**: Note: Use classful network addresses (not CIDR) for RIP’s `network` command.


## 4. Optional: Disable Auto-Summarization
```bash
no auto-summary
```
> **Note**: Prevents automatic summarization at major network boundaries.


## 5. Optional: Passive Interface (Disable RIP Updates on Selected Interfaces)
```bash
passive-interface GigabitEthernet0/0
```
> **Note**: Prevents RIP advertisements on interfaces that don’t need to send/receive routing updates (e.g., LANs).


## 6. Verify RIP Configuration
```bash
show ip protocols
show ip route rip
```
