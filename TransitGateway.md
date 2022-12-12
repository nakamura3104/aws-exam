# Overview
 - VPC, DX, VPN などを相互接続できるサービス。
 - リージョナルサービス。（別リージョンのVPCなどにアタッチはできない）
 - VPC Peeringとしても使える。（5VPC以上やSite-to-Site VPNと相互接続する場合はTGWのほうが良さそう）
   - [Transit GatewayとVPC Peeringを比較してみた](https://dev.classmethod.jp/articles/different-from-vpc-peering-and-transit-gateway-japanese/)

### 参考
 - [AWS Transit Gatewayを構築して分かったこと・ベストプラクティスを紐解く](https://blog.serverworks.co.jp/transit-gateway-best-practice)
 - [Transit Gatewayのルーティング仕様を分かりやすく解説してみる](https://blog.serverworks.co.jp/tech/2020/06/30/transit-gateway-routing/)
 - [AWS VGW / DGW / TGW の比較](https://www.megaport.com/ja/blog/aws-vgw-vs-dgw-vs-tgw/)
 - [第６回「Direct Connect Gateway、Transit Gatewayは何に注意して、どう使えばいいの？」](https://atbex.attokyo.co.jp/blog/detail/40/)
 - [Transit GatewayやDirect ConnectのAS番号について](https://blog.serverworks.co.jp/tech/2020/07/01/asn-with-dx-transitgateway/)

# 料金
 - Transit Gateway の所有者に時間単位の請求
   - `VPNアタッチメント`
   - `Transit Gateway Connect アタッチメント (SD-WAN アプライアンス)`
   - `ピア機能のアタッチメント（Transit GWピアリング）` (ほかの Transit Gateway とのピア機能アタッチメントに対して時間単位の請求)
 - Direct Connect Gateway の所有者に時間単位の請求
   - `AWS Direct Connect アタッチメント`

# 詳細仕様・構築時の注意
 - VPCとのアタッチは、ENI経由となり、専用のサブネットを作成することが推奨。
   - VPCアタッチメントサブネットのルートテーブルはTGW用のルートテーブルになってしまうため。
 - 送信元IPが自VPCのアタッチメントのENIのIPになる等、暗黙的な仕様があるため、ネットワークACLはフルオープンにする。
 - 共有サービスVPCなどをTGW経由で中継させる場合は、アプライアンスモードの有効化が必須となる。
   - TGWは、VPC間の通信において、同じAZ間で実施される。
   - 共有サービスVPCなどを介在させると、`(1)起点EC2(AZ1)`→`(2)共有VPC`→`(3)終点EC2(AZ2)`のように1と2でAZが異なる場合、1からの行きの通信と、3からの戻りの通信で共有VPCの宛先となるAZがことなり、非対称ルーティングとなってしまう。
   - https://dev.classmethod.jp/articles/enable-appliance-mode-when-using-transit-gatewayto-aggregate-communication-to-vpc-for-inspection/
 
 ### ルート伝搬
 - VPCアタッチメントの場合、伝播を有効にしていればCIDRは自動的にプロパゲートされる。
 - ただし、VPC内のルートテーブルに定義されているStaticは、TGWのルートテーブルにはプロパゲートされない。
   - https://docs.aws.amazon.com/ja_jp/vpc/latest/tgw/how-transit-gateways-work.html
 
# Transit GW Connect
> - [Transit Gateway Connect アタッチメントと Transit Gateway Connect ピア](https://docs.aws.amazon.com/ja_jp/vpc/latest/tgw/tgw-connect.html)
> - [Simplify SD-WAN connectivity with AWS Transit Gateway Connect](https://aws.amazon.com/jp/blogs/networking-and-content-delivery/simplify-sd-wan-connectivity-with-aws-transit-gateway-connect/)
- SD-WAN製品などを複数のVPCと接続したい場合に、仮想アプライアンス（EC2）とTGWが直接GRE&BGPで接続できる機能。
![img](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2020/12/10/tgw-high-level-architecture-fig-1-v1.png)

# TGWをフルメッシュで接続する意味
- TGWをリング型で接続しても、Peering間のルーティングはStaticであるため、あるリージョンの障害時の迂回のような構成はできない。
- そのため、TGW同士をフルメッシュで接続し、それぞれ直結しているリージョンに1ホップでルーティングできるような経路設計とする必要がある。
> [AWS DX – DXGW with AWS Transit Gateway, Multi-Regions (more than 3)](https://docs.aws.amazon.com/whitepapers/latest/hybrid-connectivity/aws-dx-dxgw-with-aws-transit-gateway-multi-regions-more-than-3.html)

![img](https://docs.aws.amazon.com/images/whitepapers/latest/hybrid-connectivity/images/dx-dxgw-transit-gateway-multi-regions.png)
