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

## VyOS => AWS の経路をMain系優先に
 - Weight属性を使う
> 特定のパスを選択するために使用します。 Weightは、ルータローカルでのみ使用され、他のBGPルータとは交換されません。 同じプレフィックスに対して、値が大きい方のルートを優先します。
 
```
vyos@vyos# set protocols bgp neighbor 169.254.234.49 address-family ipv4-unicast weight 300
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

> AWS側から見たMain（オレンジの線）のInが増加
![image](https://user-images.githubusercontent.com/60680996/203072448-2f758754-491d-46f2-abf6-fa8c5319dd0f.png)


