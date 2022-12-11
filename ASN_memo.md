# 受験体験記
- https://blog.serverworks.co.jp/I-passed-ans-c01-exam
- https://qiita.com/go_sougen/items/43dae9a62cf1c8356548
- https://qiita.com/the13-HK/items/349cdf5f38e0ce1e47b1
- https://qiita.com/aminosan000/items/dfea62d2c6cc2763d15c

***
# Hands-On
 - [Site-to-Site VPN (Single)](https://github.com/nakamura3104/aws-exam/blob/main/VyOS.md)

 - s3エンドポイント＋エンドポイントポリシーでのプライベート接続
   - （エンドポイントポリシーというものを認識していなかった）
 - オンプレからアクセスできるもの、できないも、やり方
 - オンプレミスDNSフォワーダからプライベートホストゾーンの名前解決
 - [この資料](https://techstock.jp/wp-content/uploads/20200219_BlackBelt_Onpremises_Redundancy.pdf)の冗長化方法、可能な限り
 - CF+S3で署名付きリクエストを使って特定のユーザにのみコンテンツを配信する。
 - CludHubを使ったVGW経由のサイト間通信
 - Private Link
 - Gateway Load Balancer
 - Global Accelarator

<br>

***
# Routingの優先順位
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
 - CloudFrontは、リクエストの最初のバイトを受信したらファイルをユーザに転送し始める仕様。
 - web socketに対応
 - error 504：オリジンにアクセスできない、応答が遅い
 - error 403：CNAMEを設定していない、ディストリビューションが無効
 - udpは使えない。当たり前だけど。httpなのだから。
 - DirectConnectによりオンプレにオリジンサーバを配置することも可能。
 
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
# VPC Flow log
## フローログで記録されないトラフィック
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

## NLB Proxy protocol v2
`ALBにおけるXFFのような、クライアントのIPを保持する機能`
 - NLBはターゲットをインスタンスIDで指定すれば、クライアントのIPが保持される。
 - しかし、ターゲットをIPで指定した場合、NLBのIPが送信元となる。
しらべる


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

### Gateway方式とInterface方式
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

### VPC エンドポイントポリシー
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
