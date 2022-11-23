# Overview
 - VPC, DX, VPN などを相互接続できるサービス。
 - リージョナルサービス。（別リージョンのVPCなどにアタッチはできない）
 - VPC Peeringとしても使える。（5VPC以上やSite-to-Site VPNと相互接続する場合はTGWのほうが良さそう）
   - [Transit GatewayとVPC Peeringを比較してみた](https://dev.classmethod.jp/articles/different-from-vpc-peering-and-transit-gateway-japanese/)

# 料金
 - Transit Gateway の所有者に時間単位の請求
   - `VPNアタッチメント`
   - `Transit Gateway Connect アタッチメント (SD-WAN アプライアンス)`
   - `ピア機能のアタッチメント（Transig GWピアリング）` (ほかの Transit Gateway とのピア機能アタッチメントに対して時間単位の請求)
 - Direct Connect Gateway の所有者に時間単位の請求
   - `AWS Direct Connect アタッチメント`

 
