# Huawei STP Configuration – Multi-Switch Example


## 1. Enter System View
```bash
system-view
```


## 2. Set STP Mode to RSTP
```bash
stp mode rstp
```
> **Note**: Rapid Spanning Tree Protocol enables faster convergence than standard STP.


## 3. Configure Switch1 as Root Bridge
```bash
stp instance 0 priority 4096
```
> **Note**: Lower priority makes this switch the preferred root for instance 0 (default).


## 4. Configure Switch2 as Secondary Root
```bash
stp instance 0 priority 8192
```
> **Note**: Will become root if Switch1 fails.


## 5. Configure Uplink Ports (Switch2, Switch3)
```bash
interface GigabitEthernet0/0/1
description Uplink to upstream switch
stp edge disable
```
> **Note**: Uplink ports must not be edge ports.


## 6. Configure Access Ports as Edge (All Switches)
```bash
interface range GigabitEthernet0/0/2 to GigabitEthernet0/0/24
stp edge enable
```
> **Note**: Edge ports transition to forwarding state immediately. Use only on host-facing ports.


## 7. Enable BPDU Guard on Access Ports
```bash
interface range GigabitEthernet0/0/2 to GigabitEthernet0/0/24
stp bpdu-protection enable
```
> **Note**: Protects edge ports from rogue switch connections. Port is shut down on BPDU reception.



## 8. Enable Root Guard on Uplink Ports (Switch1)
```bash
interface GigabitEthernet0/0/1
stp root-protection enable
```
> **Note**: Prevents another switch from becoming root via this port.



## 8. Verify STP Status
```bash
display stp
display stp brief
```
> **Note**: Shows bridge roles, port states, priorities, and timers.