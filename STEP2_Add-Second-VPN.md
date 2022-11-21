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

## AWS（VGW）の優先順位
1. ロンゲストマッチ（宛先ネットワークがより詳細に絞り込まれているルート）
2. DirectConnectとSite to Site VPNではDirectConnectが優先
3. Site to Site VPNで静的ルートとBGPルートでは、静的ルートが優先
4. BGPルートの中ではAS PASHが最短のものが優先
5. AS PATHが同じ場合はMEDが最小のものが優先

## 検証
 - 初期状態
   - AWS側から通知されるMED属性（Metric）が200であるBackup経路（vti1）が優先される。[参考](https://dev.classmethod.jp/articles/control-bgp-route-on-site-to-site-vpn/)
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

## [TE1]VyOS => AWS の経路をMain系優先に
 - Weight属性を使う
> 特定のパスを選択するために使用します。 Weightは、ルータローカルでのみ使用され、他のBGPルータとは交換されません。 同じプレフィックスに対して、値が大きい方のルートを優先します。
 
```
set protocols bgp neighbor 169.254.234.49 address-family ipv4-unicast weight 300
```

 - 変更後の状態
```
vyos@vyos:~$ show ip bgp
～～
   Network          Next Hop            Metric LocPrf Weight Path
*> 10.0.2.0/24      0.0.0.0                  0         32768 i
*  10.99.0.0/16     169.254.61.113         100             0 64512 i
*>                  169.254.234.49         200           300 64512 i  ##weight 300
```

> AWS側から見たIn方向のMain経路（オレンジの線）にトラフィックが切り替わる
![image](https://user-images.githubusercontent.com/60680996/203072448-2f758754-491d-46f2-abf6-fa8c5319dd0f.png)


## [TE2]VyOS <= AWS の経路をMain系優先に
- AS PATH Prependを使う
> 広報するルートにAS PATHを追加することで相手に遠い経路情報と見せる。

```
set policy route-map VPC-Tunnel-2-OUT rule 10 action permit
set policy route-map VPC-Tunnel-2-OUT rule 10 set as-path prepend 65000
set protocols bgp neighbor 169.254.61.113 address-family ipv4-unicast route-map export VPC-Tunnel-2-OUT
```

- 変更後の状態
```
//Main
vyos@vyos:~$ show ip bgp ipv4 unicast neighbors 169.254.234.49 advertised-routes
~~
   Network          Next Hop            Metric LocPrf Weight Path
*> 10.0.2.0/24      0.0.0.0                  0         32768 i
*> 10.99.0.0/16     0.0.0.0                              300 64512 i

Total number of prefixes 2



//Backup
vyos@vyos:~$ show ip bgp ipv4 unicast neighbors 169.254.61.113 advertised-routes
~~
   Network          Next Hop            Metric LocPrf Weight Path
*> 10.0.2.0/24      0.0.0.0                  0         32768 65000 i
*> 10.99.0.0/16     0.0.0.0                              300 65000 64512 i

Total number of prefixes 2
```

> AWS側から見たOut方向のMain経路（水色の線）にトラフィックが切り替わる
![image](https://user-images.githubusercontent.com/60680996/203080705-201bd86f-c72a-4ed3-892b-ceaa71295113.png)

# 完成系

![image](https://user-images.githubusercontent.com/60680996/203082426-d63901e2-7316-421b-bca8-1cf906cbfc07.png)

<details>
<summary>config</summary>

 ```
 set interfaces ethernet eth0 address 'dhcp'
set interfaces ethernet eth0 hw-id '08:00:27:ca:37:b8'
set interfaces ethernet eth1 hw-id '08:00:27:f5:29:18'
set interfaces ethernet eth2 address '169.254.153.1/16'
set interfaces ethernet eth2 description 'Host-Only'
set interfaces ethernet eth2 hw-id '08:00:27:ae:2e:6a'
set interfaces loopback lo
set interfaces vti vti0 address '169.254.234.50/30'
set interfaces vti vti0 description 'VPC tunnel 1'
set interfaces vti vti0 mtu '1436'
set interfaces vti vti1 address '169.254.61.114/30'
set interfaces vti vti1 description 'VPC tunnel 2'
set interfaces vti vti1 mtu '1436'
set policy route-map VPC-Tunnel-2-OUT rule 10 action 'permit'
set policy route-map VPC-Tunnel-2-OUT rule 10 set as-path prepend '65000'
set protocols bgp address-family ipv4-unicast network 10.0.2.0/24
set protocols bgp neighbor 169.254.61.113 address-family ipv4-unicast route-map export 'VPC-Tunnel-2-OUT'
set protocols bgp neighbor 169.254.61.113 address-family ipv4-unicast soft-reconfiguration inbound
set protocols bgp neighbor 169.254.61.113 remote-as '64512'
set protocols bgp neighbor 169.254.61.113 timers holdtime '30'
set protocols bgp neighbor 169.254.61.113 timers keepalive '10'
set protocols bgp neighbor 169.254.234.49 address-family ipv4-unicast soft-reconfiguration inbound
set protocols bgp neighbor 169.254.234.49 address-family ipv4-unicast weight '300'
set protocols bgp neighbor 169.254.234.49 remote-as '64512'
set protocols bgp neighbor 169.254.234.49 timers holdtime '30'
set protocols bgp neighbor 169.254.234.49 timers keepalive '10'
set protocols bgp system-as '65000'
set service ssh
set system config-management commit-revisions '100'
set system conntrack modules ftp
set system conntrack modules h323
set system conntrack modules nfs
set system conntrack modules pptp
set system conntrack modules sip
set system conntrack modules sqlnet
set system conntrack modules tftp
set system console device ttyS0 speed '115200'
set system host-name 'vyos'
set system login user vyos authentication encrypted-password '$6$NY2pqN1he4xk.Jdf$CHTp6hnMJr.SHxgQXX2ec6kc2iqMVEvyCjKvZVSTPZ7Cq3vMu6kgtDeczPFA4.K2jRAIWsb9Td3/YOFMNK63F/'
set system login user vyos authentication plaintext-password ''
set system ntp server time1.vyos.net
set system ntp server time2.vyos.net
set system ntp server time3.vyos.net
set system syslog global facility all level 'info'
set system syslog global facility protocols level 'debug'
set system time-zone 'Asia/Tokyo'
set vpn ipsec esp-group AWS lifetime '3600'
set vpn ipsec esp-group AWS mode 'tunnel'
set vpn ipsec esp-group AWS pfs 'enable'
set vpn ipsec esp-group AWS proposal 1 encryption 'aes128'
set vpn ipsec esp-group AWS proposal 1 hash 'sha1'
set vpn ipsec ike-group AWS dead-peer-detection action 'restart'
set vpn ipsec ike-group AWS dead-peer-detection interval '15'
set vpn ipsec ike-group AWS dead-peer-detection timeout '30'
set vpn ipsec ike-group AWS lifetime '28800'
set vpn ipsec ike-group AWS proposal 1 dh-group '2'
set vpn ipsec ike-group AWS proposal 1 encryption 'aes128'
set vpn ipsec ike-group AWS proposal 1 hash 'sha1'
set vpn ipsec site-to-site peer AWS authentication local-id '110.134.213.239'
set vpn ipsec site-to-site peer AWS authentication mode 'pre-shared-secret'
set vpn ipsec site-to-site peer AWS authentication pre-shared-secret '8UvZ3un9O64TjRtFN47VILNyz1LP0rrz'
set vpn ipsec site-to-site peer AWS authentication remote-id '52.194.154.6'
set vpn ipsec site-to-site peer AWS description 'VPC tunnel 1'
set vpn ipsec site-to-site peer AWS ike-group 'AWS'
set vpn ipsec site-to-site peer AWS local-address '10.0.2.15'
set vpn ipsec site-to-site peer AWS remote-address '52.194.154.6'
set vpn ipsec site-to-site peer AWS vti bind 'vti0'
set vpn ipsec site-to-site peer AWS vti esp-group 'AWS'
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
 ```
</details>
 
