# 0. Overview
### Diagram

![image](https://user-images.githubusercontent.com/60680996/202910379-119406d1-4860-49bf-ac27-c70684acbc16.png)



# 1. Add Secondary VPN Config
### VyOS Config
```
## IPSec
set vpn ipsec site-to-site peer AWS_backup authentication local-id '110.134.213.239'
set vpn ipsec site-to-site peer AWS_backup authentication mode 'pre-shared-secret'
set vpn ipsec site-to-site peer AWS_backup authentication pre-shared-secret '8dBpXucERWygH7ymX0VWzZx1gCQboPav'
set vpn ipsec site-to-site peer AWS_backup authentication remote-id '54.248.82.123'
set vpn ipsec site-to-site peer AWS_backup description 'VPC tunnel 2'
set vpn ipsec site-to-site peer AWS_backup ike-group 'AWS'
set vpn ipsec site-to-site peer AWS_backup local-address '10.0.2.15'
set vpn ipsec site-to-site peer AWS_backup remote-address '54.248.82.123'
set vpn ipsec site-to-site peer AWS_backup vti bind 'vti1'
set vpn ipsec site-to-site peer AWS_backup vti esp-group 'AWS'

## VTI
set interfaces vti vti1 address '169.254.61.114/30'
set interfaces vti vti1 description 'VPC tunnel 2'
set interfaces vti vti1 mtu '1436'

## BGP Config
set protocols bgp neighbor 169.254.61.113 address-family ipv4-unicast soft-reconfiguration inbound
set protocols bgp neighbor 169.254.61.113 remote-as '64512'
set protocols bgp neighbor 169.254.61.113 timers holdtime '30'
set protocols bgp neighbor 169.254.61.113 timers keepalive '10'
```

# 2. Traffic Engineering
 - 初期状態
   - MED属性（Metric）が200であるBackup経路（vti1）が優先される。
   - Backup経路をshutdown→復旧してもトラフィックが戻ってくる。
   
```
vyos@vyos:~$ show ip bgp ipv4 unicast
BGP table version is 9, local router ID is 10.0.2.15, vrf id 0
Default local pref 100, local AS 65000
Status codes:  s suppressed, d damped, h history, * valid, > best, = multipath,
               i internal, r RIB-failure, S Stale, R Removed
Nexthop codes: @NNN nexthop's vrf id, < announce-nh-self
Origin codes:  i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

   Network          Next Hop            Metric LocPrf Weight Path
*> 10.0.2.0/24      0.0.0.0                  0         32768 i
*> 10.99.0.0/16     169.254.61.113         100             0 64512 i
*                   169.254.234.49         200             0 64512 i

Displayed  2 routes and 3 total paths
```
