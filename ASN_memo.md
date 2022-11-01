# 受験体験記
https://blog.serverworks.co.jp/I-passed-ans-c01-exam

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


・S3が「HTTP 403: Access Denied」となる理由
　・AWS アカウント全体のバケットおよびオブジェクトを所有する所有者のアクセス許可
　　→S3オブジェクトの所有者はdefaultではアップロードしたAWSアカウントとなる。
　　　自分のバケットに他のアカウントがアップ可能ば場合、このようなアクセス不可が起きる。

　・バケットポリシーまたは AWS Identity and Access Management (IAM) ユーザーポリシー上の問題

　・Amazon S3 にアクセスするためのユーザー認証情報
　　→AWS SDK&AWS CLI は、IAM ユーザーの認証情報、またはバケットへの
　　　アクセス許可のあるIAMロールを使用するように設定されている必要がある。

　・VPC エンドポイントポリシー
　・ブロックパブリックアクセスの設定
　・存在しないオブジェクト
　　→「404 Not Found」ではなく403エラーで隠す仕様となっている。

　・AWS KMSによるオブジェクトの暗号化
　　→KMSで暗号化されている場合、IAMのアクセス権限以外に
　　　KMSキーポリシーで暗号化/復号化等のactionが許可されている必要がある。

　・バケットで有効化されているリクエスタ支払い
　　→バケットをリクエスタ支払いバケットとして設定すると、匿名でのアクセスが403errorとなる。
　　　リクエスタは、リクエストに x-amz-request-payer を (POST、GET、HEADの場合はヘッダーに、RESTの場合はパラメータとして) 含めることで、
　　　リクエストとデータのダウンロードに課金されることを了解している旨を示す必要がある

　・AWS Organizations のサービスコントロールポリシー
　　→IAMポリシーだけでなくSCPでも許可されている必要がある

・Cloud Front 詳細
　－web socketに対応
　－error 504：オリジンにアクセスできない、応答が遅い
　－error 403：CNAMEを設定していない、ディストリビューションが無効
　－udpは使えない。当たり前だけど。httpなのだから。
　－DirectConnectによりオンプレにオリジンサーバを配置することも可能。


・VPCエンドポイント
　Gateway方式とInterface方式があり、Gateway方式があるのはS3とDynamoだけ
　－Gateway方式
　　ルートテーブルのターゲットとして登録される。（IGWのような感じ）
　　エンドポイントにアクセスするインスタンス側のSGでの制御が必要。
　　VPC外からGWへのアクセスは出来ない。
　　－VPCエンドポイントポリシーの割当が出来る
　　　エンドポイントにアタッチ出来るIAMリソースポリシー。
　　　エンドポイントから指定されたサービス(S3/Dynamo)へのアクセスを制限できる。
　　　他のポリシー（S3の場合、IAMユーザポリシーやバケットポリシー）を上書きするものではない。（and条件で許可が必要）

　－Interface方式（別名Privatelink）
　　プライベートIPを持つENIとして登録される。
　　エンドポイント自体にSGを割り当ててIN/OUTを制限できる。
　　DirectConnectの場合だけ、VPC外からのアクセスが可能。

・特殊なインスタンスタイプ
　　P3    汎用 GPU インスタンスの最新世代です。
　　P2    汎用 GPU コンピューティングアプリケーション用に設計されています。
　　G4    機械学習推論やグラフィックを大量に使用するワークロードを迅速化するために設計されています。
　　G3    グラフィック集約型アプリケーション用に最適化されています。
　　F1    フィールドプログラマブルゲートアレイ (FPGA) によるカスタマイズ可能なハードウェアアクセラレーションが提供されま
　　→ネットワークアプライアンスのレイテンシを最小化するにはF1が最適

・EC2の準仮想化(PV)と完全仮想化(HVM)の違い
　PVはネットワークやディスクIOをエミュレーションせず、特別なドライバで操作する方式
　HVMは物理マシンのOSイメージをそのまま利用可能
　従来はPVのほうが性能がよかったが、最近はPV on HVMによりHVMでも同等以上の性能が可能
　https://awsjp.com/AWS/hikaku/paravirtualPV-Hardware-assistedVM-compare.html


・TransitVPC
　CiscoCSRとかlambdaを利用した自作TransitGatewayみたいなもの。

・CloudHUB
　VGWを中継点として複数のCGWをハブスポークで相互通信できるようにしたもの。CGWのAS番号が重複しないようにする必要がある。



・DHCPオプションセット
　EC2とかの起動時にIPと一緒に払い出すパラメータ一式。
　[domain-name-servers]
	最大4つまでのDNSサーバー、または AmazonProvidedDNSのIP アドレス。
	デフォルトのDHCPオプションセットでAmazonProvidedDNS が指定。
	複数のドメインネームサーバーを指定する場合は、カンマで区切る。
　[domain-name]
	us-east-1でAmazonProvidedDNS を使用している場合は、ec2.internalとなる。
	別のリージョンで AmazonProvidedDNSを使用している場合は、region.compute.internalとなる（例: ap-northeast-1.compute.internal）。
	それ以外の場合は、ドメイン名を指定する (例: example.com)。
　[ntp-servers]
	最大 4 つまでのNTPサーバーのIPアドレス。デフォルトは、Amazon Time Sync Serviceで、リンクローカルの169.254.169.123出来る。
　[netbios-name-servers]
	最大 4 つまでの NetBIOS ネームサーバーの IP アドレス。
　[netbios-node-type]
	NetBIOS ノードタイプ (1、2、4、8)。

　→NTPサーバを変更するには、新しいオプションセットを作成し、VPCの設定で割り当てられているオプションセットを変更する。

・DNS関連
【DNS一般】
・フォワーダ
　自分自身ではDNSの名前解決を行わず、別のフルサービスリゾルバへDNS要求を中継するDNSサーバ
　結果のキャッシュは行う。LANからインターネットへのクエリトラフィックを削減するために用いられる。

・CNAME
　(CNAMEレコードの例)---------------------------------------------
　server01.example.jp.   IN  A      192.168.20.31
　www1.example.jp.       IN  CNAME  server01.example.jp.
　www2.example.jp.       IN  CNAME  server01.example.jp.
　-----------------------------------------------------------------
　・別名に対し、正式名であるcinonicalレコード（server01）は、1つでなければならない。
　　上記の例だと、wwwは増やせるが、例えばwww1に対し複数のserver01、02みたいな指定はできない。

　・別のレコードがあるのにCNAMEを登録することはできない。
　　上記の例だとserver01を別名としたCNAMEは登録できない。

・Zone Apex（ゾーンの頂点）
　権威サーバが持つゾーンそのもののこと。
　この名前にはCNAMEを設定できない。それは同一の名前があるレコードのCNAMEは作成できないという制約に対して
　zone apexは、example.com NS <name server>というNSレコードを持つことがRFCで定められているため。
　https://www.atmarkit.co.jp/fnetwork/dnstips/008.html

・SRVレコード
　ドメイン内の共用的なサーバとかのIP/ポート番号を提供するレコード。
　SRVレコードに対応するクライアントであれば、使いたいサーバの情報を管理者に問い合わせることなく入手できる

 SRVレコードのフォーマット
 _Service._Proto.Name  TTL Class  SRV Priority  Weight  Port  Target
　(SRVレコードの例)------------------------------------------------
　_ftp._tcp.example.jp.   IN  SRV 1   0   21  server01.example.jp.
　_ftp._tcp.example.jp.   IN  SRV 2   0   21  server02.example.jp.
　server01.example.jp.    IN  A   192.168.20.31
　server02.example.jp.    IN  A   192.168.20.32
　-----------------------------------------------------------------


【AmazonProvidedDNS】
　・VPCのネットワークアドレスに2を加えたIPを持つ。
　・キャッシュサーバとしてのみ利用可能。
　・セットしたVPCのAmazonDNS以外からはアクセスできない。


【PrivateHostedとPablicHosted】
　－Privateは、オリジナルのローカルドメインを作成できる。
　　AmazonDNSで割り当てられる名前以外を使いたいときに利用する。
　　たとえば、パブリックのfqdnと同じものを使いたいがクライアントがVPCにいるときはVPCのIPを応答させたいなど。
　
・Amazon DNSサーバ（AmazonProvidedDNS）
　－AWSにより提供されるキャッシュサーバ（フルリゾルバ）。
　－VPC内からしか利用できない。
　－ルートヒント（ルートサーバのIP一覧）の編集不可。
　－フォワーダの編集不可
　－AWSとしては、IPとして169.254.169.253も使用可能だが、WindowsServer2008などでは使えない。

・Directory Service Microsoft AD
　AWS上に構築できるフルマネージドのADサーバ。



・VPC Flowlog
　L7レベルは見れない。（L4＋α）


・ALB　　
　- パスベース：リクエスト内のURLに基づいて、ターゲットグループへのルートを設定
　- ホストベース：要求されたドメインに基づいて、ターゲットグループへのルートを設定

・AWS inspector
　EC2に対して脆弱性診断ができるツール。エージェントが必要。以下のOSに対応。
　－Amazon Linux (2015.03 以降)
　－Ubuntu (14.04 LTS)
　－Red Hat Enterprise Linux (7.2)
　－CentOS (7.2)
　－Windows Server 2008 R2 および Windows Server 2012（β版）

・AWSでのパケットキャプチャ
　VPC flow log 以外だとVPC Traffic monitoringという機能がる。
　これはVPC内にTAPを構成し、ミラーしたパケットを解析のためのNLBやEC2に転送できる機能である。
　キャプチャしたパケットはsuricataやwiresharkで解析できる。
　　


・AWSのAD関連サービスの種類
　・ADコネクター
　　ADプロキシーとしてオンプレのADの認証やマネコンへのSSOができる

　・SimpleAD
　　LinuxベースのADサービス。そこまでガチじゃない構成ならこっち。
　
　・MicrosoftAD
　　ガチのADサービス。大規模ならこっち。

ストレージgwの最安接続方法
QinQ使えるか
ebs最適化
クラウドフロント挟める構成パターン

＜参考＞
・準仮想化と完全仮想化の違い

http://blog.serverworks.co.jp/tech/2018/12/11/concepts-of-direct-connect/
http://aws-de-media.s3.amazonaws.com/images/AWS_Summit_2018/June7/Coral/AWS%20Direct%20Connect%20Deep%20Dive.pdf


＊Ephemeral port ：はかないポート。クライアント側として動的に割り当てられる一時的なポート番号
　　　　　　　　　 RFC6056では1024以上、IANA/UNIX/Windでは49152以上、Linuxは32768～61000、WinXP/2003以前は1025-5000


＜MPLS関連翻訳＞

・VPC over MPLSへの接続を実装するにはどうすればよいですか？
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


・S3が403となる理由
vifのパブリックとプライベートの違い
dxを介した相互通信
オンプレとのdns連携
ストレージgwの最安接続方法
zone apexのレコード、alias x2か、alias.ptrか
ホストルーティング、パスルーティングの違い
dxリージョン間接続
bgpで使える属性
cloud hub
cloudfrontでudpいけるのか
ec2のntpサーバを変更する方法
dnsのtrue/host,resolv
dxの料金、マルチアカウントの場合誰に課金される
g2インスタンスとは
ec2のレイテンシをおさえる構成
拡張ネットワーキング
拡張ネットワークドライバ
mtu
s3エンドポイントに対するアクセス制御
トランジットvpc
aws workspaceの利用に必要なネットワーク要件
オンプレとのad連携
QinQ使えるか
ebs最適化
dx申請の流れと、必要な情報
albだけパブリックに見せる、可能か
レイテンシルーティングの詳細
クラウドフロント挟める構成パターン
aws inspectorとは
dpi、みるツール
flowlog、l7まで見れるのか

＜引用＞
http://blog.serverworks.co.jp/tech/2018/12/11/concepts-of-direct-connect/
http://aws-de-media.s3.amazonaws.com/images/AWS_Summit_2018/June7/Coral/AWS%20Direct%20Connect%20Deep%20Dive.pdf
