## EVPN-VXLAN Topology Overview

![Topology Diagram](https://github.com/ishikawaya/clab-lab03-evpn-vxlan/blob/main/images/topology.png)

- Routers **r[1-6]** establish eBGP adjacencies using **IPv6 BGP Unnumbered**.
- BGP is configured with the following address families enabled:
  - **IPv4 Unicast**
  - **IPv6 Unicast**
  - **L2VPN EVPN**
- For both IPv4 and IPv6 unicast address families, **connected routes are redistributed** to advertise loopback addresses.
- The **IPv4 loopback addresses** are used as VXLAN local endpoints, ensuring reachability and enabling EVPN VXLAN operation.
- Devices **r[1,3,5]** and **srv[1,3,5]** belong to **VXLAN 10**.
- Devices **r[2,4,6]** and **srv[2,4,6]** belong to **VXLAN 20**.
- Connectivity within each VXLAN segment has been verified.

### srv1 to srv3 ping result
```bash
$ docker exec -ti clab-lab03-srv1 sh -c "ping -c 3 192.168.10.129"
PING 192.168.10.129 (192.168.10.129): 56 data bytes
64 bytes from 192.168.10.129: seq=0 ttl=64 time=0.209 ms
64 bytes from 192.168.10.129: seq=1 ttl=64 time=0.240 ms
64 bytes from 192.168.10.129: seq=2 ttl=64 time=0.240 ms

--- 192.168.10.129 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.209/0.229/0.240 ms
```

### srv2 to srv4 ping result
```bash
$ docker exec -ti clab-lab03-srv2 sh -c "ping -c 3 192.168.20.129"
PING 192.168.20.129 (192.168.20.129): 56 data bytes
64 bytes from 192.168.20.129: seq=0 ttl=64 time=0.187 ms
64 bytes from 192.168.20.129: seq=1 ttl=64 time=0.167 ms
64 bytes from 192.168.20.129: seq=2 ttl=64 time=0.243 ms

--- 192.168.20.129 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.167/0.199/0.243 ms
```

### l2vpn evpn route result
```
$ docker exec -ti clab-lab03-r1 bash -c "vtysh -c 'show bgp l2vpn evpn route detail'"
Route Distinguisher: 4200000000:10
BGP routing table entry for 4200000000:10:[2]:[0]:[48]:[32:e4:df:72:24:81]:[32]:[192.168.10.5]
Paths: (2 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[32:e4:df:72:24:81]:[32]:[192.168.10.5] VNI 10
  4200000006 4200000005
    172.16.0.5 from eth2 (172.16.0.6)
      Origin IGP, valid, external, best (AS Path)
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:38 2025
  Route [2]:[0]:[48]:[32:e4:df:72:24:81]:[32]:[192.168.10.5] VNI 10
  4200000002 4200000003 4200000004 4200000005
    172.16.0.5 from eth1 (172.16.0.2)
      Origin IGP, valid, external
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:24 2025
BGP routing table entry for 4200000000:10:[2]:[0]:[48]:[32:e4:df:72:24:81]:[128]:[fe80::240a:4ff:fe5d:c26f]
Paths: (2 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[32:e4:df:72:24:81]:[128]:[fe80::240a:4ff:fe5d:c26f] VNI 10
  4200000006 4200000005
    172.16.0.5 from eth2 (172.16.0.6)
      Origin IGP, valid, external, best (AS Path)
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:38 2025
  Route [2]:[0]:[48]:[32:e4:df:72:24:81]:[128]:[fe80::240a:4ff:fe5d:c26f] VNI 10
  4200000002 4200000003 4200000004 4200000005
    172.16.0.5 from eth1 (172.16.0.2)
      Origin IGP, valid, external
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:24 2025
BGP routing table entry for 4200000000:10:[2]:[0]:[48]:[aa:c1:ab:1f:53:16]
Paths: (1 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[aa:c1:ab:1f:53:16] VNI 10
  4200000006 4200000005
    172.16.0.5 from eth2 (172.16.0.6)
      Origin IGP, valid, external, best (First path received)
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:38:33 2025
BGP routing table entry for 4200000000:10:[2]:[0]:[48]:[aa:c1:ab:b6:44:ae]:[32]:[192.168.10.3]
Paths: (2 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[aa:c1:ab:b6:44:ae]:[32]:[192.168.10.3] VNI 10
  4200000002 4200000003
    172.16.0.3 from eth1 (172.16.0.2)
      Origin IGP, valid, external, best (AS Path)
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:05 2025
  Route [2]:[0]:[48]:[aa:c1:ab:b6:44:ae]:[32]:[192.168.10.3] VNI 10
  4200000006 4200000005 4200000004 4200000003
    172.16.0.3 from eth2 (172.16.0.6)
      Origin IGP, valid, external
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:38 2025
BGP routing table entry for 4200000000:10:[2]:[0]:[48]:[aa:c1:ab:b6:44:ae]:[128]:[fe80::78d5:acff:fe1e:241b]
Paths: (2 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[aa:c1:ab:b6:44:ae]:[128]:[fe80::78d5:acff:fe1e:241b] VNI 10
  4200000002 4200000003
    172.16.0.3 from eth1 (172.16.0.2)
      Origin IGP, valid, external, best (AS Path)
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:05 2025
  Route [2]:[0]:[48]:[aa:c1:ab:b6:44:ae]:[128]:[fe80::78d5:acff:fe1e:241b] VNI 10
  4200000006 4200000005 4200000004 4200000003
    172.16.0.3 from eth2 (172.16.0.6)
      Origin IGP, valid, external
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:38 2025
BGP routing table entry for 4200000000:10:[3]:[0]:[32]:[172.16.0.1]
Paths: (1 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [3]:[0]:[32]:[172.16.0.1] VNI 10
  Local
    172.16.0.1 from 0.0.0.0 (172.16.0.1)
      Origin IGP, weight 32768, valid, sourced, local, best (First path received)
      Extended Community: ET:8 RT:4200000000:10
      Last update: Sat Dec  6 11:24:59 2025
      PMSI Tunnel Type: Ingress Replication, label: 10
BGP routing table entry for 4200000000:10:[3]:[0]:[32]:[172.16.0.3]
Paths: (2 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [3]:[0]:[32]:[172.16.0.3]
  4200000002 4200000003
    172.16.0.3 from eth1 (172.16.0.2)
      Origin IGP, valid, external, best (AS Path)
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:05 2025
      PMSI Tunnel Type: Ingress Replication, label: 10
  Route [3]:[0]:[32]:[172.16.0.3]
  4200000006 4200000005 4200000004 4200000003
    172.16.0.3 from eth2 (172.16.0.6)
      Origin IGP, valid, external
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:38 2025
      PMSI Tunnel Type: Ingress Replication, label: 10
BGP routing table entry for 4200000000:10:[3]:[0]:[32]:[172.16.0.5]
Paths: (2 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [3]:[0]:[32]:[172.16.0.5]
  4200000006 4200000005
    172.16.0.5 from eth2 (172.16.0.6)
      Origin IGP, valid, external, best (AS Path)
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:38 2025
      PMSI Tunnel Type: Ingress Replication, label: 10
  Route [3]:[0]:[32]:[172.16.0.5]
  4200000002 4200000003 4200000004 4200000005
    172.16.0.5 from eth1 (172.16.0.2)
      Origin IGP, valid, external
      Extended Community: RT:4200000000:10 ET:8
      Last update: Sat Dec  6 11:26:24 2025
      PMSI Tunnel Type: Ingress Replication, label: 10
Route Distinguisher: 4200000000:20
BGP routing table entry for 4200000000:20:[2]:[0]:[48]:[4e:51:b0:ff:f8:6d]:[32]:[192.168.20.4]
Paths: (2 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[4e:51:b0:ff:f8:6d]:[32]:[192.168.20.4] VNI 20
  4200000002 4200000003 4200000004
    172.16.0.4 from eth1 (172.16.0.2)
      Origin IGP, valid, external, best (Older Path)
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:26:12 2025
  Route [2]:[0]:[48]:[4e:51:b0:ff:f8:6d]:[32]:[192.168.20.4] VNI 20
  4200000006 4200000005 4200000004
    172.16.0.4 from eth2 (172.16.0.6)
      Origin IGP, valid, external
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:26:38 2025
BGP routing table entry for 4200000000:20:[2]:[0]:[48]:[4e:51:b0:ff:f8:6d]:[128]:[fe80::600f:40ff:fef1:9012]
Paths: (2 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[4e:51:b0:ff:f8:6d]:[128]:[fe80::600f:40ff:fef1:9012] VNI 20
  4200000002 4200000003 4200000004
    172.16.0.4 from eth1 (172.16.0.2)
      Origin IGP, valid, external, best (Older Path)
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:26:12 2025
  Route [2]:[0]:[48]:[4e:51:b0:ff:f8:6d]:[128]:[fe80::600f:40ff:fef1:9012] VNI 20
  4200000006 4200000005 4200000004
    172.16.0.4 from eth2 (172.16.0.6)
      Origin IGP, valid, external
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:26:38 2025
BGP routing table entry for 4200000000:20:[2]:[0]:[48]:[82:99:ba:85:49:45]:[32]:[192.168.20.6]
Paths: (1 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[82:99:ba:85:49:45]:[32]:[192.168.20.6] VNI 20
  4200000006
    172.16.0.6 from eth2 (172.16.0.6)
      Origin IGP, valid, external, best (First path received)
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:26:38 2025
BGP routing table entry for 4200000000:20:[2]:[0]:[48]:[82:99:ba:85:49:45]:[128]:[fe80::868:89ff:fe5f:19dc]
Paths: (1 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[82:99:ba:85:49:45]:[128]:[fe80::868:89ff:fe5f:19dc] VNI 20
  4200000006
    172.16.0.6 from eth2 (172.16.0.6)
      Origin IGP, valid, external, best (First path received)
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:26:38 2025
BGP routing table entry for 4200000000:20:[2]:[0]:[48]:[aa:c1:ab:42:fc:9d]:[32]:[192.168.20.2]
Paths: (1 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[aa:c1:ab:42:fc:9d]:[32]:[192.168.20.2] VNI 20
  4200000002
    172.16.0.2 from eth1 (172.16.0.2)
      Origin IGP, valid, external, best (First path received)
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:25:36 2025
BGP routing table entry for 4200000000:20:[2]:[0]:[48]:[aa:c1:ab:42:fc:9d]:[128]:[fe80::4897:c5ff:fe27:eb73]
Paths: (1 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [2]:[0]:[48]:[aa:c1:ab:42:fc:9d]:[128]:[fe80::4897:c5ff:fe27:eb73] VNI 20
  4200000002
    172.16.0.2 from eth1 (172.16.0.2)
      Origin IGP, valid, external, best (First path received)
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:25:36 2025
BGP routing table entry for 4200000000:20:[3]:[0]:[32]:[172.16.0.2]
Paths: (1 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [3]:[0]:[32]:[172.16.0.2]
  4200000002
    172.16.0.2 from eth1 (172.16.0.2)
      Origin IGP, valid, external, best (First path received)
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:25:36 2025
      PMSI Tunnel Type: Ingress Replication, label: 20
BGP routing table entry for 4200000000:20:[3]:[0]:[32]:[172.16.0.4]
Paths: (2 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [3]:[0]:[32]:[172.16.0.4]
  4200000002 4200000003 4200000004
    172.16.0.4 from eth1 (172.16.0.2)
      Origin IGP, valid, external, best (Older Path)
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:26:12 2025
      PMSI Tunnel Type: Ingress Replication, label: 20
  Route [3]:[0]:[32]:[172.16.0.4]
  4200000006 4200000005 4200000004
    172.16.0.4 from eth2 (172.16.0.6)
      Origin IGP, valid, external
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:26:38 2025
      PMSI Tunnel Type: Ingress Replication, label: 20
BGP routing table entry for 4200000000:20:[3]:[0]:[32]:[172.16.0.6]
Paths: (1 available, best #1)
  Advertised to peers:
  eth1 eth2
  Route [3]:[0]:[32]:[172.16.0.6]
  4200000006
    172.16.0.6 from eth2 (172.16.0.6)
      Origin IGP, valid, external, best (First path received)
      Extended Community: RT:4200000000:20 ET:8
      Last update: Sat Dec  6 11:26:38 2025
      PMSI Tunnel Type: Ingress Replication, label: 20

Displayed 17 prefixes (26 paths)
```
