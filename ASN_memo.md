# 受験体験記
- https://blog.serverworks.co.jp/I-passed-ans-c01-exam
- https://qiita.com/go_sougen/items/43dae9a62cf1c8356548
- https://qiita.com/the13-HK/items/349cdf5f38e0ce1e47b1
- https://qiita.com/aminosan000/items/dfea62d2c6cc2763d15c

***
# Hands-On
 - [Site-to-Site VPN (Single)](https://github.com/nakamura3104/aws-exam/blob/main/VyOS.md)

 - SourceとDestinationがそれぞれ別VPCのトラフィックミラーリングと、ミラーリングしたパケットのフィルタ。
 - Private Link
　　 - エンドポイントサービスのオンプレからの名前解決
 - Shared VPCでの別VPCのNAT-GWを使った通信
 - Gateway Load Balancer
   - GWLBだけ別VPCでの単一VPCの監査
   - TGWありで複数VPCを監査 
 - RAMでのNATGWの共用
 - s3エンドポイント＋エンドポイントポリシーでのプライベート接続
   - （エンドポイントポリシーというものを認識していなかった）
 - オンプレからアクセスできるもの、できないも、やり方
 - オンプレミスDNSフォワーダからプライベートホストゾーンの名前解決
 - CF+S3で署名付きリクエストを使って特定のユーザにのみコンテンツを配信する。
 - Global Accelarator

<br>

*** 
# 低遅延なネットワーク環境を実現するサービス

## Wavelength Zone
[AWS Wavelength のよくある質問](https://aws.amazon.com/jp/wavelength/faqs/)
- キャリア（日本ではKDDI）のDC内に設けられたAWSのコンピューティングゾーン。
- デバイスと10msec以内のレイテンシで通信したいようなアプリに向いている。
- デプロイできるサービスは、EC2やEBS。

## Outposts
[AWS Outposts に必要なネットワーク接続を理解しよう](https://dev.classmethod.jp/articles/aws-outposts-network-connectivity/)
- 利用者側のネットワーク機器2台ｘAWS側のデバイス2台でそれぞれLAGで接続
- Service Linkは、AWS側との接続
- Local Gatewayは、オンプレ側との接続
- 上記の2つの用途でそれぞれVLANが必要
- AWS側、ローカル側ともにBGPが必要
- AWS側とのWANは、DXもしくはインターネットで、AWS側のパブリックアドレスレンジと通信する。
- Outposts内のインスタンスとAWS側の通信は、内部的なVPN経由となる。
- マネジメントコンソール的には、1つの専用AZが作成された感じとなる。

<br>

***

# VPC

## VPC Ingress Routing
 - > [VPC Ingress Routingとは何かを噛み砕いて理解してみる](https://dev.classmethod.jp/articles/what-is-vpc-ingress-routing/)

 - 特定のゲートウェイにルートテーブルを関連付けられるようになった
   - インターネットゲートウェイ（IGW）と仮想プライベートゲートウェイ（VGW）である
   - 上記の関連付けをエッジアソシエーションと呼ぶ
   - エッジアソシエーションされたルートテーブルをゲートウェイルートテーブル と呼ぶ

- ゲートウェイルートテーブルには以下の制限がある
  - 送信先として指定できるのはVPCのCIDRの範囲内のみ
  - ターゲットに指定できるのは「local」か「ネットワークインタフェース」のみ
  - 「ルート伝達」が有効であってはならない

- ゲートウェイルートテーブルによって、VPCへのインバウンドを細かくルーティングできるようになった
  - 送信先IPとは異なるインタフェースにルーティングが可能に
  - ルーティング先のサーバでIPフォワードされる設定がされていることが必要
  - ルーティング先のEC2で「送信元/送信先チェック」は無効であることが必要

## Shared VPC
- VPCのサブネットを別アカウントに共有する機能
- 共有するアカウントが親、共有されるアカウントが子の関係となり、Organizations内でないと共有できない。
- VPCE、FWE、NAT-GWなどのインフラデバイスは、専用サブネット（共有できない）である必要がある。

## VPC Flow log
### フローログで記録されないトラフィック
 - Amazon DNS サーバーに接続したときにインスタンスによって生成されるトラフィック。独自の DNS サーバーを使用する場合は、その DNS サーバーへのすべてのトラフィックが記録されます。
 - Amazon Windows ライセンスのアクティベーション用に Windows インスタンスによって生成されたトラフィック。
 - インスタンスメタデータ用に 169.254.169.254 との間を行き来するトラフィック。
 - Amazon Time Sync Service の 169.254.169.123 との間でやり取りされるトラフィック。
 - DHCP トラフィック。
 - ミラートラフィック。
 - デフォルト VPC ルーターの予約済み IP アドレスへのトラフィック。
 - エンドポイントのネットワークインターフェイスと Network Load Balancer のネットワークインターフェイスの間のトラフィック。

<br>

***

# Private Link

## プライベートDNS名
> - [2020.01.07 [新機能] PrivateLinkの公開サービスにプライベートDNS名が指定可能になりました](https://dev.classmethod.jp/articles/private-dns-name-for-privatelink/)
> - [2021.02.03 オンプレミスからプライベートネットワークでのアクセスが可能に！PrivateLink に Amazon S3 が追加されました
](https://dev.classmethod.jp/articles/private-link-for-s3/)

![img](https://d1tlzifd8jdoy4.cloudfront.net/wp-content/uploads/2020/01/pvlink-pv-dns-000.png)
- PrivateLink（エンドポイントサービス）のFQDNを自身が所有しているパブリックドメインのレコードとして公開できる。
- 解決されるIPは当然プライベートIP
- ドメインの認証のためTXTレコードの登録が必要
- オンプレからのDX/VPNでの接続の場合は、この機能は使えない。（無効にする必要がある）



***
<br>

# Routingの優先順位
## DX
1. ロンゲストマッチ
2. Static
3. BGP
   1. AS-PATH
   2. 生成元（IGP→EGP→IGP再配送）
   3. ルータID
   4. ネイバーのID

## DX and VPN on VGW
1. ロンゲストマッチ
2. DXとVPNでは、DXを優先
3. VPNでBGPとStaticではStaticが優先
4. BGPの中ではAS Path
5. AS Pathが同じ場合はMED

## TGW
1. Static
2. プレフィックスリスト参照ルート
3. VPC が伝達したルート
4. Direct Connect ゲートウェイが伝達したルート
5. Transit Gateway Connect (GRE Tunnel)が伝達したルート
6. Site-to-Site VPN 伝達ルート

***
# AWSで利用できるBGPコミュニティ属性
 - BGPコミュニティ属性とは、管理者が任意に意味付けを定義できる、そのルートに対してのポリシー。
 - AWSでは、以下のコミュニティ属性が定義されており、それによりAWSからの受信トラフィックの制御や、オンプレ側からの広報したルートのAWS内での伝播範囲などを制御できる。
 
## パブリック仮想インターフェイス BGP コミュニティ
> Amazon ネットワークでプレフィックスを伝播する範囲を示すことができます。
 - `7224:9100` —ローカル AWS リージョン
 - `7224:9200` —大陸のすべての AWS リージョン
 - `7224:9300` —グローバル (すべてのパブリック AWS リージョン)

> AWS Direct Connect は、アドバタイズされたルートに次の BGP コミュニティを適用します。
 - `7224:8100` — AWS Direct Connect ポイント オブ プレゼンスが関連付けられている同じ AWS リージョンから発信されるルート。
 - `7224:8200` — AWS Direct Connect ポイント オブ プレゼンスが関連付けられているのと同じ大陸から始まるルート。



## プライベート仮想インターフェイスとトランジット仮想インターフェイスの BGP コミュニティ
> コミュニティ タグを適用して、戻りトラフィックの関連パスの優先順位を示すことができます。
 - `7224:7100` —低選好
 - `7224:7200` —中程度の好み
 - `7224:7300` — 高選好


***
<br>

# Gateway Load Balancer
### 参考Link
 - [Geneve](https://blog.shin.do/2014/05/geneve-encapsulation/)
 - [Checkpoint](https://blog.checkpoint.com/2020/11/10/check-point-cloudguard-integrates-with-aws-gateway-load-balancer-at-launch/)


# Nat-GW
 - IPフラグメントをサポートしていない。

<br>

***
# CloudHSM
 - KMSの専用環境版のイメージ。
 - VPC内にHSM用のインスタンスを作成し、そこに鍵を保管し、参照する。
 - HSMインスタンスはクラスター構成を取ることが可能。

### ユースケース
https://www.digicert.co.jp/welcome/pdf/wp_ssl_negotiation.pdf

 - Webサーバ側でSSLを終端する場合に、その暗号化・復号化をオフロードできる。
 - SSL証明書認証のフロー
   - ![image](https://user-images.githubusercontent.com/60680996/201510093-bc8a3c1f-5dd0-4cb2-8947-251c056bd453.png)

 - CloudHSMでオフロードする場合のフロー
   - ![img](https://docs.aws.amazon.com/ja_jp/cloudhsm/latest/userguide/images/ssl-offload-handshake-process.png)


<br>

***
# CloudFront
## Overview
 - CloudFrontは、リクエストの最初のバイトを受信したらファイルをユーザに転送し始める仕様。
 - web socketに対応
 - error 504：オリジンにアクセスできない、応答が遅い
 - error 403：CNAMEを設定していない、ディストリビューションが無効
 - udpは使えない。当たり前だけど。httpなのだから。
 - DirectConnectによりオンプレにオリジンサーバを配置することも可能。
 
## CDN一般
### キャッシュさせない設定(Cache-Controlヘッダー)
`private`
- レスポンスがひとりのユーザーのため(private)のものであり、共有キャッシュに保存してはいけない。 つまり、プライベートなんだからそもそもキャッシュするのは論外！！

`no-cache`
- オリジンサーバーの確認無しに勝手にキャッシュしてはいけない。つまり、勝手に再利用するな！毎回聞きに来い！ノー、キャッシュ！！

`no-store`
- リクエスト、レスポンスをキャッシュしてはいけない。つまり、キャッシュに記録するんじゃないぞ！ノー、ストア！！

> CFは、上記を設定していてもオリジンと通信できない場合、キャッシュを返してしまう。
> https://docs.aws.amazon.com/ja_jp/AmazonCloudFront/latest/DeveloperGuide/Expiration.html
> ```
> オリジンが接続不能で、さらに最小 TTL が 0 より大きい場合、CloudFront は、先にオリジンから取得済みのオブジェクトを返信します。
> この動作を回避するには、Cache-Control: stale-if-error=0 ディレクティブに、オリジンから返されたオブジェクトを含めます。
> このようにすることで、オリジンが接続不能な場合に CloudFront が以後のリクエストに応答する際、以前にオリジンから取得したオブジェクトを返すのではなくエラーを返すようになります。
> ```


## CF2とLambda@Edge
  - ヘッダの追加・変更など軽い処理はCF2で実装する。
  - 複雑な処理、オリジンとのやり取りにて必要な処理はLambda@Edge。
  - ![image](https://user-images.githubusercontent.com/60680996/206914213-2c237cd2-b21b-4451-a259-83d8ea169417.png)
  - https://dev.classmethod.jp/articles/amazon-cloudfront-functions-release/
 
 ## Origin Shield
 > [Amazon CloudFrontにオリジンへのリクエストを軽減するキャッシュレイヤー(Origin Shield)が追加されました](https://dev.classmethod.jp/articles/amazon-cloudfront-support-cache-layer-origin-shield/)
 - Edge location, Reginal Edge Cache より更にOrigin側に配置できるキャッシュ。
 - グローバルに提供されるようなサービスに対し、よりキャッシュを効率化するための機能。

### 同じビューからからの2度目のリクエストはエッジキャッシュにヒット
![img](https://d1tlzifd8jdoy4.cloudfront.net/wp-content/uploads/2020/10/cache-hit-pop.png)
<br>

### 別POPの場合はリージョナルキャッシュにヒット
![img](https://d1tlzifd8jdoy4.cloudfront.net/wp-content/uploads/2020/10/cache-hit-regional.png)
<br>

### 別リージョンの場合はOrigin Shield キャッシュにヒット
![img](https://d1tlzifd8jdoy4.cloudfront.net/wp-content/uploads/2020/10/cache-hit-shield.png)
<br>
 
<br>

***
# CloudHUB
 - VGWを中継点として複数のCGWをハブスポークで相互通信できるようにしたもの。CGWのAS番号が重複しないようにする必要がある。


# DHCPオプションセット
- EC2とかの起動時にIPと一緒に払い出すパラメータ一式。
- [domain-name-servers]
  - 最大4つまでのDNSサーバー、または AmazonProvidedDNSのIP アドレス。
  - デフォルトのDHCPオプションセットでAmazonProvidedDNS が指定。
  - 複数のドメインネームサーバーを指定する場合は、カンマで区切る。
 - [domain-name]
   - us-east-1でAmazonProvidedDNS を使用している場合は、ec2.internalとなる。
   - 別のリージョンで AmazonProvidedDNSを使用している場合は、region.compute.internalとなる（例: ap-northeast-1.compute.internal）。
   - それ以外の場合は、ドメイン名を指定する (例: example.com)。
- [ntp-servers]
  - 最大 4 つまでのNTPサーバーのIPアドレス。デフォルトは、Amazon Time Sync Serviceで、リンクローカルの169.254.169.123出来る。
- [netbios-name-servers]
  - 最大 4 つまでの NetBIOS ネームサーバーの IP アドレス。
- [netbios-node-type]
  - NetBIOS ノードタイプ (1、2、4、8)。

- →NTPサーバを変更するには、新しいオプションセットを作成し、VPCの設定で割り当てられているオプションセットを変更する。


***

# WorkSpaces
## ユーザ側のデバイス・通信要件
- サポートするクライアントデバイス (PC、Mac、Linux、iPad、Android タブレット、または Android 対応 Chrome OS デバイス)
- TCP ポート 443 と PCoIP 用 4172 または WSP 用 4195 のいずれか、および PCoIP 用 UDP ポート 4172 または WSP 用 4195 が開いているインターネット接続
   - ※内→外アクセスの事らしい 
 - 　VPCへの要件として2つのプライベートサブネット、2つのパブリックサブネット、NATゲートウェイ又はEIPが必要。
   - （OSインストールやアプリのデプロイのためWorkspaceからインターネットへのアクセスが必要）
 - ユーザ認証のためのディレクトリサービスが必要
   - AWS上に新規の場合は、Microsoft AD or Simple AD
   - オンプレADと連携する場合、AD Connector
 - 利用にはクライアントソフトが必要
   - クライアント側のネットワークでTCP/UDP4172,HTTPS,RTT<100msの要件を満たしている必要がある。

***
・インスタンスから自分のメタデータを取得する方法
<http://169.254.169.254/latest/meta-data/>

・cfn-init
　cloudFormationの拡張スクリプト

***
# ELB
## ALB
- [Quiz1 no.7](https://www.whizlabs.com/learn/course/aws-advanced-networking-speciality/195/quiz/60111/Practice/start)
  - クライアントIPでリスナールールを設定したい場合、XFFヘッダーを直接指定することはできない。http-headerで指定する。
> ```
> [
>   {
>       "Field": "http-header",
>       "HttpHeaderConfig": {
>           "HttpHeaderName": "User-Agent",
>           "Values": ["*Chrome*", "*Safari*"]
>       }
>   }
> ]
> ```


## NLB 
### Proxy protocol v2
`ALBにおけるXFFのような、クライアントのIPを保持する機能`
 - NLBはターゲットをインスタンスIDで指定すれば、クライアントのIPが保持される。
 - しかし、ターゲットをIPで指定した場合、NLBのIPが送信元となる。
しらべる

### ALBをTargetGroupに登録する
> - [Network Load BalancerのターゲットグループにApplication Load Balancerを設定する](https://aws.amazon.com/jp/blogs/news/application-load-balancer-aws-privatelink-static-ip-addresses-network-load-balancer/)
> - [NLBのTargetにALBを指定しhttpsアクセスさせる](https://www.beex-inc.com/blog/nlb_alb_https_access)

- 2021のアップデートでNLBのTGにALBを登録できるようになった。
- 主なユースケースは、IPを固定したい場合や、ALB配下のアプリケーションをPrivateLinkで公開したい場合。

![img](https://d2908q01vomqb2.cloudfront.net/b3f0c7f6bb763af1be91d9e74eabfeb199dc1f1f/2021/09/28/Picture1-3-1.png)

***
# EC2 Network
## 拡張ネットワーキング
 - ppsの性能を高く、ネットワークジッター/レイテンシーを低くする方法
 - この機能では、従来の実装と比較し、I/O パフォーマンスが高く、CPU 利用率が低くなる新しいネットワーク仮想化スタックが使用される。
 - 拡張ネットワーキングを最大限に利用するためには、VPC で HVM AMI を起動し、適切なドライバをインストールする必要がある。

#### シングルルートI/O仮想化（SR-IOV）
 - 仮想サーバのネットワークを低遅延にすることにより高スループットを実現するための標準化された仕組み。
 - Hypervisor経由であったネットワーク処理を仮想化に対応したNIC（PCIe）にオフロードすることにより、ネットワークの処理をハードウェア化する。
 - [ネットワンの参考ブログ](https://www.netone.co.jp/knowledge-center/blog-column/knowledge_takumi_087/)


## プレイスメントグループ
 - EC2が起動するAWSのデーターセンターの物理サーバ配置を指定できる設定
 - `クラスター`、`パーティション`、`分散`の３つの戦略がある
 - `クラスター`は、インスタンス間の通信を低レイテンシーにしたい場合
 - `パーティション`は、hadoopやkafkaなど、いくつかのインスタンスは低レイテンシにしつつ、それらの複数のクラスターを分散させたい場合
 - `分散`は、物理サーバのラックを分散させ耐障害性を上げたい場合
 - 同じリージョン内の複数のPeerVPCに跨ることが可能。
 - 既存のインスタンスをプレイスメントグループに追加する場合は、一度停止させaws cliで移動させる必要がある
    - > インスタンスタイプを統一し、追加に伴うインスタンス総数を再設定して追加した上で、プレイスメントグループを再起動することが必要です。既存のプレイスメントグループに新規EC2インスタンスを追加する際は、インスタンスグループ全体を一旦停止してから、新規EC2インスタンスをグループに追加して、プレイスメントグループにあるEC2インスタンスを再起動することで解決することができます
https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/placement-groups.html#change-instance-placement-group

## 特殊なインスタンスタイプ
 - `P3` 汎用 GPU インスタンスの最新世代です。
 - `P2` 汎用 GPU コンピューティングアプリケーション用に設計されています。
 - `G4` 機械学習推論やグラフィックを大量に使用するワークロードを迅速化するために設計されています。
 - `G3` グラフィック集約型アプリケーション用に最適化されています。
 - `F1` フィールドプログラマブルゲートアレイ (FPGA) によるカスタマイズ可能なハードウェアアクセラレーションが提供されま
   - > ネットワークアプライアンスのレイテンシを最小化するにはF1が最適


## EC2の準仮想化(PV)と完全仮想化(HVM)の違い
- PVはネットワークやディスクIOをエミュレーションせず、特別なドライバで操作する方式
- HVMは物理マシンのOSイメージをそのまま利用可能
- 従来はPVのほうが性能がよかったが、最近はPV on HVMによりHVMでも同等以上の性能が可能 
  - > https://awsjp.com/AWS/hikaku/paravirtualPV-Hardware-assistedVM-compare.html
  - > http://blog.serverworks.co.jp/tech/2018/12/11/concepts-of-direct-connect/
  - > http://aws-de-media.s3.amazonaws.com/images/AWS_Summit_2018/June7/Coral/AWS%20Direct%20Connect%20Deep%20Dive.pdf

***
# S3
### S3が「HTTP 403: Access Denied」となる理由
 - AWS アカウント全体のバケットおよびオブジェクトを所有する所有者のアクセス許可
   - →S3オブジェクトの所有者はdefaultではアップロードしたAWSアカウントとなる。
   - 自分のバケットに他のアカウントがアップ可能ば場合、このようなアクセス不可が起きる。
 - バケットポリシーまたは AWS Identity and Access Management (IAM) ユーザーポリシー上の問題

### Amazon S3 にアクセスするためのユーザー認証情報
 - AWS SDK&AWS CLI は、IAM ユーザーの認証情報、またはバケットへのアクセス許可のあるIAMロールを使用するように設定されている必要がある。


### バケットで有効化されているリクエスタ支払い
- バケットをリクエスタ支払いバケットとして設定すると、匿名でのアクセスが403errorとなる。
- リクエスタは、リクエストに `x-amz-request-payer`を (POST、GET、HEADの場合はヘッダーに、RESTの場合はパラメータとして) 含めることで、リクエストとデータのダウンロードに課金されることを了解している旨を示す必要がある

***
# VPC Endpoint

![privatelink](https://devlog.arksystems.co.jp/2018/05/11/4896/)

## Gateway方式とInterface方式
- Gateway方式
  - 対応しているのはS3とDynamoだけ
  - VPCエンドポイントはリージョン毎の設定となる
  - ルートテーブルのターゲットとして登録される。（IGWのような感じ）
  - VPC外からGWへのアクセスは出来ない。
  - GWの方は、名前解決するとパブリックIPが返答される。
  - VPCエンドポイントポリシーの割当が出来る

- Interface方式（別名Privatelink）
  - プライベートIPを持つENIとして登録される。
  - エンドポイント自体にSGを割り当ててIN/OUTを制限できる。
  - DirectConnectの場合だけ、VPC外からのアクセスが可能。

|              | Gateway型                                                                          | PrivateLink(Interface型)                                                                                                                   | 
| ------------ | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | 
| アクセス制御 | エンドポイントポリシー。<br>IAM Policyと同じ構文でアクセス先のリソースを制限可能。 | セキュリティグループ。<br>セキュリティグループでアクセス元IP、ポートを制御可能。<br>対象のサービスの特定のリソースへのアクセス制御は不可。 | 
| 利用料金     | 無料                                                                               | 有料                                                                                                                                       | 
| 冗長性       | ユーザー側で意識する必要なし                                                       | マルチAZで配置するように設定する                                                                                                           | 

## VPC エンドポイントポリシー
- エンドポイントの作成時または変更時にエンドポイントにアタッチする IAM リソースポリシー
- エンドポイントの作成時にポリシーをアタッチしない場合、サービスへのフルアクセスを許可するデフォルトのポリシーがアタッチされる
- 他のポリシー（S3の場合、IAMユーザポリシーやバケットポリシー）を上書きするものではない。（and条件で許可が必要）


***
# AWSのAD関連サービスの種類
- ADコネクター
  - ADプロキシとしてオンプレのADの認証やマネコンへのSSOができる

- SimpleAD
  - LinuxベースのADサービス。そこまでガチじゃない構成ならこっち。
　
- MicrosoftAD
  - ガチのADサービス。大規模ならこっち。

***
# その他
 - Ephemeral port ：はかないポート。クライアント側として動的に割り当てられる一時的なポート番号
   - RFC6056では1024以上、IANA/UNIX/Windでは49152以上、Linuxは32768～61000、WinXP/2003以前は1025-5000


ストレージgwの最安接続方法
QinQ使えるか
ebs最適化



# MPLS関連翻訳
- VPC over MPLSへの接続を実装するにはどうすればよいですか？
　ほとんどのAmazon Web Service（AWS）のお客様は、
　オンプレミスネットワークとAWSの間で確立するリモート接続の
　タイプ（VPNまたはAWS Direct Connect）をすばやく判断できます。
　マルチプロトコルラベルスイッチング（MPLS）を使用して地理的に
　分散した企業ネットワークを接続するお客様にとって、
　既存のMPLS接続を全体的なネットワーク設計に組み込む方法を理解することは困難です。
　このWebページは、Amazon Virtual Private Cloud（Amazon VPC）と
　MPLSネットワーク間の高可用性で効率的な接続の構築に役立つベストプラクティス、
　推奨事項、および一般的な構成をAWSのお客様に提供します。
                                                            
・インスタンスから自分のメタデータを取得する方法
http://169.254.169.254/latest/meta-data/



- 現時点の疑問()
  - AZ障害に対応できるNFSマウントできるマネージドサービスはあるか? EFSはAZフェイルオーバーはなさそう。
  - →いや、ある。
  - EFS自体はマルチAZで展開され、AZ毎にエンドポイントがあるから、AZ障害にも対応可能
  - ★AZ-A の EFS エンドポイントが死んだ時に AZ-C のエンドポイントに自動切り替え(再マウント)される機能は無いです。
  
  - SESでキャリアメールには問題なく送付可能? EC2からメール送付する場合、レピュテーション対策は必須?
    - →可能。レピュテーションは、迷惑メール送る様なメールサーバを立てるつもりなのか？
    - ★そもそも SES はバウンス率が高いと送信制限が発生するサービスなので、B2C で使用するのは危険。おとなしく SendGrid あたりをつかいましょう。
    - キャリアメールへの送信は SPF レコードをちゃんと設定すれば問題なし。

オンプレとのdns連携
ストレージgwの最安接続方法
zone apexのレコード、alias x2か、alias.ptrか
ホストルーティング、パスルーティングの違い
dxリージョン間接続
bgpで使える属性
cloud hub
ec2のntpサーバを変更する方法
dnsのtrue/host,resolv
dxの料金、マルチアカウントの場合誰に課金される
g2インスタンスとは
ec2のレイテンシをおさえる構成
拡張ネットワークドライバ
mtu
aws workspaceの利用に必要なネットワーク要件
オンプレとのad連携
QinQ使えるか
ebs最適化
dx申請の流れと、必要な情報
albだけパブリックに見せる、可能か
レイテンシルーティングの詳細
クラウドフロント挟める構成パターン
dpi、みるツール
flowlog、l7まで見れるのか

＜引用＞
http://blog.serverworks.co.jp/tech/2018/12/11/concepts-of-direct-connect/
http://aws-de-media.s3.amazonaws.com/images/AWS_Summit_2018/June7/Coral/AWS%20Direct%20Connect%20Deep%20Dive.pdf
