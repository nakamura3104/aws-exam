# ToDo Learn

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

# Route 53 DNS Firewall
- Route53 Resolverでの名前解決に適用できるDNS Firewall。
- ドメインリストとアクション（Allow,Block,Alert）を定義する。

<br>

***
# DNSSEC
## 参考リンク
 - [NSECレコードとは](https://hnw.hatenablog.com/entry/20160327#:~:text=NSEC%E3%83%AC%E3%82%B3%E3%83%BC%E3%83%89%E3%81%A8%E3%81%AF,%E3%81%99%E3%82%8B%E3%82%88%E3%81%86%E3%81%AA%E4%BB%95%E7%B5%84%E3%81%BF%E3%81%A7%E3%81%99%E3%80%82)
 - [【図解】初心者にも分かりそうなDNSSECの仕組みと機能,シーケンス～キャッシュポイズニングと対策,普及/対応状況,KSKとZSKを分ける意義～](https://milestone-of-se.nesuke.com/l7protocol/dns/dnssec-summary/)

## 概要
 - DNSキャッシュポインズニングというクエリに対しての応答の改ざん（正規のドメインで不正なサーバにアクセスさせる）を防ぐための仕組み。
 - `公開鍵/秘密鍵によるデジタル署名`技術を使っているが、形式は https のような`X.509`ではなく、DNSSEC 専用のフォーマットが使われる。
 - 当該の権威サーバからDNSKEYレコードで公開鍵を、RRSIGレコードで当該レコードの署名を入手し、応答内容と応答相手を検証する。


## 用語
`ZSK(ゾーン署名鍵)とKSK(鍵署名鍵)`
- リソースレコードを署名するための鍵。クライアント（DNSキャッシュサーバ）は、DNSKEYレコードで取得することができる。
- 高い暗号化強度と運用性（暗号・復号の負荷軽減）を両立するために上位権威サーバに登録が必要なKSKと通常使用するZSKという2つに分けている。
- [ZSK](https://jprs.jp/glossary/index.php?ID=0233)
  - そのゾーンのリソースレコードの署名に使われるため、権威サーバの負荷を下げるために短い鍵長で作成される。
  - 短い鍵長のためローテーションを頻繁に行う必要がある。
- [KSK](https://jprs.jp/glossary/index.php?ID=0232)
  - ZSKとKSKのDNSKEYレコードの署名するための鍵。
  - `ZSK公開鍵`のDNSKEYリソースレコードをゾーンに登録し、`KSK秘密鍵`で署名することにより、当該ゾーン内におけるZSKの真正を証明する。
  - KSKの真正は、KSKのDNSKEYレコードを上位の権威サーバのDSレコードとして申請・登録することで信頼チェーンを構築する。
  

`DNSKEYレコード`
- DNSSEC検証に用いる公開鍵を格納するためのリソースレコード。([detail](https://jprs.jp/glossary/index.php?ID=0214))
- ![img](https://jprs.jp/glossary/imgs/DNSKEY-RR.png)
  - flag： ZSKであれば256を、KSKであれば257を指定します。
  - protocol： DNSSECを示すプロトコル番号である3を指定します。
  - algorithm： DNSSECのアルゴリズム番号（※）を定義し、RSASHA256の場合は8を指定します。
  - public key： Base64という方式で符号化した公開鍵の内容を記述します。

`RRSIGレコード`
- リソースレコードに対する電子署名を格納するレコード。別途取得したそのゾーンのDNSKEYリソースレコードと併せて応答を検証する。([detail](https://jprs.jp/glossary/index.php?ID=0224))

`DSレコード`
- 子ゾーンのDNSKEYレコードの真正を証明するためのレコード
- 子のDNSKEY（＝公開鍵）のHashを親にDSレコードとして登録することで、このゾーン（ZSK,KSK）の真正を証明する。
- ![img](https://jprs.jp/related-info/guide/topics-column/18/img_5.jpg)

***
<br>

# Route53 Resolver (旧Amazon Provided DNS）
- VPCのネットワークアドレスに2を加えたIPを持つ。
- キャッシュサーバとしてのみ利用可能。（フルリゾルバ）。
- セットしたVPCのAmazonDNS以外からはアクセスできない。（VPC内からしか利用できない）
- ルートヒント（ルートサーバのIP一覧）の編集不可。
- フォワーダの編集不可
- AWSとしては、IPとして169.254.169.253も使用可能だが、WindowsServer2008などでは使えない。

***
<br>

# Route53 Resolver for Hyblid Clouds
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
