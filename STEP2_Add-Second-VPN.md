# 0. Overview
### Diagram

![image](https://user-images.githubusercontent.com/60680996/202908290-70a32468-25b2-4794-bd12-c01dc151149a.png)



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

