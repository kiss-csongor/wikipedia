# Cisco STP Configuration – Multi-Switch Example


## 1. Enter Global Configuration Mode
```bash
enable
configure terminal
```


## 2. Set STP Mode to Rapid-PVST
```bash
spanning-tree mode rapid-pvst
```
> **Note**: Rapid-PVST offers faster convergence and per-VLAN STP instances.


## 3. Configure Switch1 as Root Bridge
```bash
spanning-tree vlan 1 priority 4096
```
> **Note**: Lower priority increases the chance of becoming the Root Bridge.


## 4. Configure Switch2 as Secondary Root
```bash
spanning-tree vlan 1 priority 8192
```
> **Note**: Acts as backup root if Switch1 fails.


## 5. Configure Uplink Ports (Switch2, Switch3)
```bash
interface GigabitEthernet0/1
description Uplink to upstream switch
spanning-tree link-type point-to-point
```
> **Note**: Ensures proper port role negotiation and faster transition to forwarding


## 6. Configure Access Ports as Edge (All Switches)
```bash
interface range GigabitEthernet0/2 - 24
spanning-tree portfast
```
> **Note**: Speeds up port activation for end devices. Only use on host-facing ports.


## 7. Enable BPDU Guard on Access Ports
```bash
interface range GigabitEthernet0/2 - 24
spanning-tree bpduguard enable
```
> **Note**: Shuts down the port if a BPDU is received – prevents rogue switch connections.



## 8. Optional: Enable Root Guard on Uplink Ports (Switch1)
```bash
interface GigabitEthernet0/1
spanning-tree guard root
```
> **Note**: Prevents another switch from becoming the root bridge through this port.



## 8. Verify STP Status
```bash
show spanning-tree
show spanning-tree vlan 1
```
> **Note**: Confirm root bridge, port roles, and STP state across VLANs.