# Quiz

- [x] 3
- [x] 7
- [x] 9
- [x] 14
- [x] 15
- [x] 16
- [x] 19
- [x] 20
- [x] 23
- [x] 24
- [x] 26
- [x] 41
- [x] 42
- [x] 43
- [ ] 44
- [x] 46
- [x] 47
- [x] 49
- [ ] 50
- [x] 51
- [ ] 52
- [x] 53
- [x] 54
- [x] 55
- [ ] 58
- [ ] 59
- [ ] 63

> [No.18](https://www.whizlabs.com/learn/course/aws-advanced-networking-speciality/195/quiz/59967/Practice/start)

プライベートホストゾーンのエイリアスレコードをなぜ、サービス名にする必要があるのか
https://aws.amazon.com/jp/blogs/networking-and-content-delivery/integrating-aws-transit-gateway-with-aws-privatelink-and-amazon-route-53-resolver/

***

# Arch Pattern
## Route53とTGWによるハイブリッドクラウドのDNS集中管理
> [Centralized DNS management of hybrid cloud with Amazon Route 53 and AWS Transit Gateway](https://aws.amazon.com/jp/blogs/networking-and-content-delivery/centralized-dns-management-of-hybrid-cloud-with-amazon-route-53-and-aws-transit-gateway/)

![img](https://d2908q01vomqb2.cloudfront.net/5b384ce32d8cdef02bc3a139d4cac0a22bb029e8/2019/05/03/image1-1.png)

> 想定要件
- 複数アカウント＆オンプレでRoute53 ResolverやPrivate Hostzoneを共用したい。

> 設計概要
- VPN接続を1つにまとめるため、VPN接続とVPC間接続は、Transit GWを使う。
- Route53 Private HostzoneをデプロイするアカウントのVPC（HUB VPC）では、RAMでホストゾーンを共用する。
- スポークVPCでは、RAMで共有されたPrivate HostzoneをVPCにアタッチすることで、名前解決できるようにする。
- オンプレミスとの名前解決は、必要に応じてInbound/Outboundエンドポイントを使う。

## 複数テナントのハイブリッド接続
> [Traffic Segmentation Options in AWS Direct Connect](https://d1.awsstatic.com/architecture-diagrams/ArchitectureDiagrams/traffic-segmentation-aws-direct-connect-ra.pdf)

- TGWと接続しなければ、Private VIFでそれぞれのVPCにL2延伸できる。（スライド２）
- TGWと接続する場合、オンプレ側がGREに対応していれば、単一のTransit VIFで接続しつつ、GREでL3プレーンを分割できる。（スライド３）
- TGWと接続しつつもオンプレ側がGRE対応してない場合、Public VIFをセグメントごとに作成し、PVIF毎にSite-to-Site VPNを接続、VPNごとにルートテーブルを分割する。（スライド４）
  - この場合、Transit VIFではなく、Public VIFであることに注意！（TGWとしてはVPN接続と接続となり、VIF経由の接続ではない）

