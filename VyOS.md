# STEP 1
Connect to VyOS using SSH via HOST-Only Adaptor

### Virtual Box
* Enable LAN3 as Host Only Adaptor

### VyOS
* Config
```
set interfaces ethernet eth2 address '169.254.153.1/16'
set interfaces ethernet eth2 description 'HOST-Only'
set interfaces ethernet eth2 hw-id '08:00:27:80:0f:e7'
```

# STEP 2
Connect to Internet

### Virtual Box
* Enable LAN1 as NAT

### VyOS
* Config
```
//ADD
set interfaces ethernet eth0 address 'dhcp'

//Routing Table
vyos@vyos:~$ show ip route
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR, f - OpenFabric,
       > - selected route, * - FIB route, q - queued route, r - rejected route

S>* 0.0.0.0/0 [210/0] via 10.0.2.2, eth0, 00:00:11
C>* 10.0.2.0/24 is directly connected, eth0, 00:00:13
C>* 169.254.0.0/16 is directly connected, eth2, 00:07:14

//Ping
vyos@vyos:~$ ping 8.8.8.
/bin/ping: unknown host
vyos@vyos:~$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8): 56 data bytes
64 bytes from 8.8.8.8: icmp_seq=0 ttl=56 time=27.054 ms
64 bytes from 8.8.8.8: icmp_seq=1 ttl=56 time=17.119 ms
^C--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 packets
```
