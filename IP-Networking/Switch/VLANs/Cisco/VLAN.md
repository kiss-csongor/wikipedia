# Cisco VLAN Configuration – 2 Switches with Trunk Link


## 1. Enter Global Configuration Mode on Both Switches
```bash
enable
configure terminal
```


## 2. Create VLANs on Both Switches
```bash
vlan 10
  name Sales
vlan 20
  name HR
```
> **Note**: VLANs must be created on both switches for trunking to work correctly.


## 3. Configure Trunk Port (on both switches)
```bash
interface GigabitEthernet0/1
  switchport mode trunk
  switchport trunk allowed vlan 10,20
  switchport nonegotiate
```
> **Note**: `nonegotiate` disables DTP. Both sides must be set to trunk manually.


## 4. Assign Access Ports to VLANs (on Switch1)
```bash
interface range GigabitEthernet0/2 - 5
  switchport mode access
  switchport access vlan 10

interface range GigabitEthernet0/6 - 9
  switchport mode access
  switchport access vlan 20
```
> **Note**: These ports are now assigned to VLAN 10 (Sales) and VLAN 20 (HR).


## 5. Assign Access Ports to VLANs (on Switch2)
```bash
interface range GigabitEthernet0/2 - 5
  switchport mode access
  switchport access vlan 10

interface range GigabitEthernet0/6 - 9
  switchport mode access
  switchport access vlan 20
```
> **Note**: Same VLAN-port assignments as on Switch1.


## 6. Verify Trunk Port Status
```bash
show interfaces trunk
```
> **Note**: Confirms trunking is active and shows allowed VLANs.


## 7. Verify VLAN Configuration
```bash
show vlan brief
```