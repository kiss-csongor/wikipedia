# Huawei IPv4 Static Routing Configuration


## 1. nter System View
```bash
system-view
```


## 2. Static IPv4 Route – Next-Hop IP
```bash
ip route-static 192.168.2.0 255.255.255.0 192.168.1.2
```
> **Note**: Use this when the next-hop IP is directly reachable from the local router.


## 3. Static IPv4 Route – Exit Interface Only
```bash
ip route-static 192.168.2.0 255.255.255.0 GigabitEthernet0/0/1
```
> **Note**: Suitable on point-to-point links. May lead to ARP issues on multi-access networks.


## 4. Static IPv4 Route – Exit Interface + Next-Hop IP
```bash
ip route-static 192.168.2.0 255.255.255.0 GigabitEthernet0/0/1 192.168.1.2
```
> **Note**: Most specific and recommended method on broadcast networks.


## 5. Default IPv4 Route – Next-Hop IP
```bash
ip route-static 0.0.0.0 0.0.0.0 192.168.1.254
```
> **Note**: Used to forward all traffic for unknown destinations to a gateway router.


## 6. Default IPv4 Route – Interface + Next-Hop IP
```bash
ip route-static 0.0.0.0 0.0.0.0 GigabitEthernet0/0/1 192.168.1.254
```
> **Note**: Preferred for connected broadcast segments; avoids recursive lookup.


## 7. Verify Routing Configuration
```bash
display ip routing-table
```
> **Note**: Confirm that routes are installed and reachable. Look for route codes like `S` (static) or `S*` (default static).