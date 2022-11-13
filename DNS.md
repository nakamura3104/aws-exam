# 一般知識
### フォワーダ
- 自分自身ではDNSの名前解決を行わず、別のフルサービスリゾルバへDNS要求を中継するDNSサーバ
- 結果のキャッシュは行う。LANからインターネットへのクエリトラフィックを削減するために用いられる。

### CNAME
```
　(CNAMEレコードの例)---------------------------------------------
　server01.example.jp.   IN  A      192.168.20.31
　www1.example.jp.       IN  CNAME  server01.example.jp.
　www2.example.jp.       IN  CNAME  server01.example.jp.
　-----------------------------------------------------------------
 ```
- 別名に対し、正式名であるcinonicalレコード（server01）は、1つでなければならない。
- > 上記の例だと、wwwは増やせるが、例えばwww1に対し複数のserver01、02みたいな指定はできない。

- 別のレコードがあるのにCNAMEを登録することはできない。
- > 上記の例だとserver01を別名としたCNAMEは登録できない。

### Zone Apex（ゾーンの頂点）
- 権威サーバが持つゾーンそのもののこと。
- この名前にはCNAMEを設定できない。
- それは同一の名前があるレコードのCNAMEは作成できないという制約に対してzone apexは、`example.com NS <name server>`というNSレコードを持つことがRFCで定められているため。
  - > https://www.atmarkit.co.jp/fnetwork/dnstips/008.html

### SRVレコード
- ドメイン内の共用的なサーバとかのIP/ポート番号を提供するレコード。
- SRVレコードに対応するクライアントであれば、使いたいサーバの情報を管理者に問い合わせることなく入手できる
- SRVレコードのフォーマット
```
 _Service._Proto.Name  TTL Class  SRV Priority  Weight  Port  Target
　(SRVレコードの例)------------------------------------------------
　_ftp._tcp.example.jp.   IN  SRV 1   0   21  server01.example.jp.
　_ftp._tcp.example.jp.   IN  SRV 2   0   21  server02.example.jp.
　server01.example.jp.    IN  A   192.168.20.31
　server02.example.jp.    IN  A   192.168.20.32
　-----------------------------------------------------------------
```
***

# Amazon Provided DNS
- VPCのネットワークアドレスに2を加えたIPを持つ。
- キャッシュサーバとしてのみ利用可能。（フルリゾルバ）。
- セットしたVPCのAmazonDNS以外からはアクセスできない。（VPC内からしか利用できない）
- ルートヒント（ルートサーバのIP一覧）の編集不可。
- フォワーダの編集不可
- AWSとしては、IPとして169.254.169.253も使用可能だが、WindowsServer2008などでは使えない。

***
<br>

# Route53 Resolver
- オンプレからAWS内の名前を解決できるリゾルバ/フォワーダ機能
- プライベート空間のみ（オンプレかAWSのリソースの名前解決、AWSからオンプレのリソースの名前解決）可能
- inboud方向とoutbound方向があり、エンドポイントとしてのIPが提供され、inboudの場合はそのIPをリゾルバ/フォワーダと指定することでオンプレからAmazonDNS→Route53と名前解決できる。
  - https://qiita.com/rotekxyz/items/585635a5ccd806b651e7
  - https://dev.classmethod.jp/cloud/aws/route53-resolver/

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
