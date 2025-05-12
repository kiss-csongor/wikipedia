# Cisco VLAN Configuration – 2 Switches with Trunk Link


## 1. Enter System View on Both Switches
```bash
system-view
```


## 2. Create VLANs on Both Switches
```bash
vlan 10
  description Sales
vlan 20
  description HR
```
> **Note**: VLANs must be created on both switches to be used in trunking and access assignments.


## 3. Configure Trunk Port (on both switches)
```bash
interface GigabitEthernet0/0/1
  port link-type trunk
  port trunk allow-pass vlan 10 20
```
> **Note**: Huawei does not use DTP, so trunking is manually defined.


## 4. Assign Access Ports to VLANs (on Switch1)
```bash
interface range GigabitEthernet0/0/2 to GigabitEthernet0/0/5
  port link-type access
  port default vlan 10

interface range GigabitEthernet0/0/6 to GigabitEthernet0/0/9
  port link-type access
  port default vlan 20
```
> **Note**: Sets access ports for VLAN 10 and VLAN 20.


## 5. Assign Access Ports to VLANs (on Switch2)
```bash
interface range GigabitEthernet0/0/2 to GigabitEthernet0/0/5
  port link-type access
  port default vlan 10

interface range GigabitEthernet0/0/6 to GigabitEthernet0/0/9
  port link-type access
  port default vlan 20
```
> **Note**: Same VLAN-port assignments as on Switch1.


## 6. Verify Trunk Port Status
```bash
display interface GigabitEthernet0/0/1
```
> **Note**: Shows port mode and allowed VLANs.


## 7. Verify VLAN Configuration
```bash
display vlan brief
```
> **Note**: Displays VLANs and associated ports.