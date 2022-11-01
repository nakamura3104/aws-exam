## Route53 Resolver
- オンプレからAWS内の名前を解決できるリゾルバ/フォワーダ機能
- プライベート空間のみ（オンプレかAWSのリソースの名前解決、AWSからオンプレのリソースの名前解決）可能
- inboud方向とoutbound方向があり、エンドポイントとしてのIPが提供され、inboudの場合はそのIPをリゾルバ/フォワーダと指定することでオンプレからAmazonDNS→Route53と名前解決できる。

https://qiita.com/rotekxyz/items/585635a5ccd806b651e7

https://dev.classmethod.jp/cloud/aws/route53-resolver/

- ALIASレコードを指定できるリソース
  - CloudFront
  - ELB
  - static-website-s3-bucket
  - 他のRoute 53リソースレコードセット

- レイテンシルーティング
  - 全世界展開しているようなサービスを複数リージョンで提供している場合、レイテンシーベースルーティングを指定することでクライアントへのレイテンシを小さくすることができる。
  - クライアントがある名前のELBにアクセスした際、レイテンシーが小さくなるようなリージョンのELBのDNS名が返却されます。

***
## Amazon Route 53 の複雑な構成でのヘルスチェックの動
https://docs.aws.amazon.com/ja_jp/Route53/latest/DeveloperGuide/dns-failover-complex-configs.html

### 要点
- Webアプリケーションなどのマルチリージョンにおける冗長化方式としてのRoute53の使い方。
- ヘルスチェック有りの加重ルーティング＋レイテンシルーティングを組み合わせることで、高可用なサービスを実現できる。
- 実現方法は以下の通り。
  - EC2やALBなど実体にルーティングするレコードをヘルスチェック有りの加重ルーティングで作成する。
  - 加重ルーティングは、同じ名前のレコードを複数作成できるため、冗長化ができる。
  - そこからさらにレイテンシルーティングをエイリアスレコードとして、Valueを先に作成した加重ルーティングにする。
    - 設定イメージ
    - ![image](https://user-images.githubusercontent.com/60680996/199223631-6ecf9f09-ed5a-4d7e-91b4-89036fdaa5e2.png) 
    - `レコード構成` 
    - ![レコード構成](https://docs.aws.amazon.com/ja_jp/Route53/latest/DeveloperGuide/images/hc-latency-alias-weighted.png)

    - `動作イメージ`
    - ![動作イメージ](https://docs.aws.amazon.com/ja_jp/Route53/latest/DeveloperGuide/images/hc-latency-alias-weighted-both-failed.png)
