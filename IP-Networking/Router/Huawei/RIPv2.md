# Huawei RIP Configuration (RIPv2)


## 1. Enter System View
```bash
system-view
```


## 2. Enable RIP and Create a RIP Process
```bash
rip 1
```
> **Note**: The number `1` is the RIP process ID (locally significant)


## 3. Set RIP Version to 2
```bash
version 2
```
> **Note**: Enables support for CIDR and discontiguous networks


## 4. Add Interfaces to RIP
```bash
network 192.168.1.0
network 192.168.2.0
```
> **Note**: Unlike Cisco, Huawei allows interface-level RIP control using `rip 1` + `network` (classful syntax still applies).


## 5. Optional: Disable Auto-Summarization
```bash
undo summary
```
> **Note**: This prevents route summarization at classful boundaries.


## 6. Optional: Configure Passive Interface
```bash
rip 1
passive-interface GigabitEthernet0/0
```
> **Note**: The interface specified here will not send RIP updates but can still receive them. Useful on interfaces like LANs.


## 7. Verify RIP Status
```bash
display rip
display ip routing-table protocol rip
```
> **Note**: Check that RIP is running and the learned routes are visible in the table.


