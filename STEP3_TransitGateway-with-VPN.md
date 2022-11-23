# Transit Gateway + VPN
 - Enable BGP ECMP

### White Paper
https://docs.aws.amazon.com/ja_jp/whitepapers/latest/aws-vpc-connectivity-options/aws-transit-gateway-vpn.html

![fig](https://docs.aws.amazon.com/ja_jp/whitepapers/latest/aws-vpc-connectivity-options/images/image5.png)

## 1. Delete AS-Path Prepend from VyOS for ECMP
### VyOS Config
```
delete protocols bgp neighbor 169.254.61.113 address-family ipv4-unicast route-map export 'VPC-Tunnel-2-OUT'
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
*> 10.0.2.0/24      0.0.0.0                  0         32768 i
*> 10.99.0.0/16     0.0.0.0                              300 64512 i

Total number of prefixes 2
```

- 2.cloud hub
- 3.direct connect gw + transit gw + vpn
- 4.dx-gw + tgw + vpn vpc マルチリージョン
- 5.dx-gw + tgw + vpn vpc クロスアカウント
