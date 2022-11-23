# Overview
 - VPC, DX, VPN などを相互接続できるサービス。
 - リージョナルサービス。（別リージョンのVPCなどにアタッチはできない）
 - VPC Peeringとしても使える。（5VPC以上やSite-to-Site VPNと相互接続する場合はTGWのほうが良さそう）
   - [Transit GatewayとVPC Peeringを比較してみた](https://dev.classmethod.jp/articles/different-from-vpc-peering-and-transit-gateway-japanese/)

### 参考
 - [AWS Transit Gatewayを構築して分かったこと・ベストプラクティスを紐解く](https://blog.serverworks.co.jp/transit-gateway-best-practice)

# 料金
 - Transit Gateway の所有者に時間単位の請求
   - `VPNアタッチメント`
   - `Transit Gateway Connect アタッチメント (SD-WAN アプライアンス)`
   - `ピア機能のアタッチメント（Transig GWピアリング）` (ほかの Transit Gateway とのピア機能アタッチメントに対して時間単位の請求)
 - Direct Connect Gateway の所有者に時間単位の請求
   - `AWS Direct Connect アタッチメント`

# 詳細仕様・構築時の注意
 - VPCとのアタッチは、ENI経由となり、専用のサブネットを作成することが推奨。
   - VPCアタッチメントサブネットのルートテーブルはTGW用のルートテーブルになってしまうため。
 - 送信元IPが自VPCのアタッチメントのENIのIPになる等、暗黙的な仕様があるため、ネットワークACLはフルオープンにする。
 - 
 
