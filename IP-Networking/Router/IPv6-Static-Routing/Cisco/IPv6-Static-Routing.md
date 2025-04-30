# Cisco IPv6 Static Routing Configuration


## 1. Enter Global Configuration Mode
```bash
enable
configure terminal
```


## 2. Static IPv6 Route – Next-Hop Only
```bash
ipv6 route 2001:db8:2::/64 2001:db8:1::2
```
> **Note**: Sends IPv6 traffic to a known next-hop router.


## 3. Static IPv6 Route – Exit Interface Only
```bash
ipv6 route 2001:db8:2::/64 GigabitEthernet0/1
```
> **Note**: Typically used on point-to-point links. Does not require neighbor discovery.


## 4. Static IPv6 Route – Interface + Next-Hop
```bash
ipv6 route 2001:db8:2::/64 GigabitEthernet0/1 2001:db8:1::2
```
> **Note**: Most complete method for link-local or shared-media links.


## 5. Default IPv6 Route – Next-Hop
```bash
ipv6 route ::/0 2001:db8:1::2
```
> **Note**: Directs all IPv6 traffic without a match to a specific router.


## 6. Default IPv6 Route – Interface + Next-Hop
```bash
ipv6 route ::/0 GigabitEthernet0/1 2001:db8:1::2
```
> **Note**: Reduces lookup overhead and ensures accurate delivery on Ethernet links.


## 7. Verify Routing Configuration
```bash
show ipv6 route
```
> **Note**: Confirm that routes are installed and reachable. Look for route codes like `S` (static) or `S*` (default static).