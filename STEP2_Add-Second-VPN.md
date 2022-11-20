# 0. Overview
### Diagram
![image](https://user-images.githubusercontent.com/60680996/202897297-814da4e2-1754-4859-bf2c-7f5e8bd9e9cd.png)


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

# 3. Configure of IPSEC
> ## VyOS 1.4から諸々設定が変更となっているため注意！

### Configration for VyOS IPSec-1
```
## ESP & IKE
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

## vti
set interfaces vti vti0 address '169.254.244.110/30'
set interfaces vti vti0 description 'VPC tunnel 1'
set interfaces vti vti0 mtu '1436'

## IPSec
set vpn ipsec site-to-site peer AWS authentication mode 'pre-shared-secret'
set vpn ipsec site-to-site peer AWS authentication pre-shared-secret 'UMKp9lYLJ1lZ1nU.CqhP7vTFHl0rcH1L'
set vpn ipsec site-to-site peer AWS description 'VPC tunnel 1'
set vpn ipsec site-to-site peer AWS ike-group 'AWS'
set vpn ipsec site-to-site peer AWS local-address '10.0.2.15'
set vpn ipsec site-to-site peer AWS remote-address '18.176.89.173'
set vpn ipsec site-to-site peer AWS vti bind 'vti0'
set vpn ipsec site-to-site peer AWS vti esp-group 'AWS'
### for NAT-T
set vpn ipsec site-to-site peer AWS authentication local-id '110.134.213.239'
set vpn ipsec site-to-site peer AWS authentication remote-id '18.176.89.173'

//BGP Config
set protocols bgp system-as '65000'
set protocols bgp neighbor 169.254.244.109 address-family ipv4-unicast soft-reconfiguration inbound
set protocols bgp neighbor 169.254.244.109 remote-as '64512'
set protocols bgp neighbor 169.254.244.109 timers holdtime '30'
set protocols bgp neighbor 169.254.244.109 timers keepalive '10'
set protocols bgp address-family ipv4-unicast network 10.0.2.0/24
```

> ## Note
>  - bgp soft reconfigreationの設定がVyOS1.2より変更となっているため注意
>    - [VyOS1.2.x系の設定で死ぬほどハマった話](https://note.com/tech_share/n/nb2cd20a433fd)
> 
> ```
> set protocols bgp 65000 neighbor 169.254.233.169 address-family ipv4-unicast soft-reconfiguration inbound
> # この事前設定により、ソフトウェアは、アップデートがインバウンド ポリシーによって受け入れられるかどうかに関係なく、受信したすべてのアップデートを変更せずに保存します。 
> # アップデートの保存は大量のメモリを要するため、可能な限り回避する必要があります。
> ```
> - VyOSでのNATトラバーサル設定
>   - [https://changineer.info/network/vyatta/vyatta_ipsec_nat.html](https://changineer.info/network/vyatta/vyatta_ipsec_nat.html)

# 4. AWS Setting
 - ルートテーブルでルート伝搬（VGWが受信したルートをルートテーブルに反映させる）を有効化

![image](https://user-images.githubusercontent.com/60680996/202896260-87468d18-a6ce-4ac7-b0be-c6e9eb1dc114.png)


# 5. 確認
### AWS => VyOS
```
:~/environment/ans-exam (master) $ ping 10.0.2.15
PING 10.0.2.15 (10.0.2.15) 56(84) bytes of data.
64 bytes from 10.0.2.15: icmp_seq=1 ttl=64 time=19.6 ms
64 bytes from 10.0.2.15: icmp_seq=2 ttl=64 time=18.2 ms
```

### VyOS => AWS
```
vyos@vyos:~$ ping 10.99.31.172 source-address 10.0.2.15
PING 10.99.31.172 (10.99.31.172) from 10.0.2.15 : 56(84) bytes of data.
64 bytes from 10.99.31.172: icmp_seq=1 ttl=254 time=16.3 ms
64 bytes from 10.99.31.172: icmp_seq=2 ttl=254 time=30.0 ms
64 bytes from 10.99.31.172: icmp_seq=3 ttl=254 time=24.4 ms
64 bytes from 10.99.31.172: icmp_seq=4 ttl=254 time=18.3 ms
```
