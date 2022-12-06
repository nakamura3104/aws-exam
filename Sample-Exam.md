## No.1
```
A company stores sensitive files in an Amazon S3 bucket. 
The company uploads files periodically from an application that runs on an Amazon EC2 instance. 
A network engineer must encrypt all the files that the company uploads to the S3 bucket in transit.

Which solution will meet these requirements?

会社は機密ファイルを Amazon S3 バケットに保存します。
同社は、Amazon EC2 インスタンスで実行されるアプリケーションから定期的にファイルをアップロードします。
ネットワーク エンジニアは、会社が転送中に S3 バケットにアップロードするすべてのファイルを暗号化する必要があります。

これらの要件を満たすソリューションはどれですか?
```
- Configure a bucket policy that denies all actions that are sent over an unencrypted connection.


> Correct. 
> The aws:SecureTransport IAM condition key checks whether a request was made over a secure connection. 
> This key, along with a deny statement, will prevent any action on an S3 bucket that is sent over an unsecure channel.
> For more information about S3 encryption in transit, see Security Best Practices for Amazon S3.
> https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html

## No.2
```
A network engineer must configure a VIF for a newly provisioned 1 Gbps AWS Direct Connect connection. 
A company will use the new VIF to access resources inside of a VPC from an on-premises network.

Which configuration values does the network engineer need to provide? (Select TWO.)

ネットワーク エンジニアは、新しくプロビジョニングされた 1 Gbps の AWS Direct Connect 接続用に VIF を構成する必要があります。
企業は新しい VIF を使用して、オンプレミス ネットワークから VPC 内のリソースにアクセスします。

ネットワーク エンジニアが提供する必要がある構成値はどれですか? (2 つ選択してください。)
```
![image](https://user-images.githubusercontent.com/60680996/201679958-1cc83b46-1165-458c-9609-4fc54eee126e.png)
![image](https://user-images.githubusercontent.com/60680996/201680054-89cf19c4-0ab0-4638-b98f-f71ac0bc1de3.png)

## No.3
```
A company plans to migrate applications from its on-premises data center to the AWS Cloud. 
The company will begin by hosting 20 applications in a single AWS Region. 
The company plans to use a dedicated AWS account for each application because of a company policy.

These applications require connectivity to the existing on-premises infrastructure for the company's application authentication service. 
Some of the applications require connectivity to other applications that the company will also move to AWS. 
After the successful migration of the 20 applications, the next migration phase will migrate 1,000 applications.

Which solution will meet these requirements with the LEAST management overhead?

ある会社は、アプリケーションをオンプレミスのデータセンターから AWS クラウドに移行することを計画しています。
同社は、単一の AWS リージョンで 20 個のアプリケーションをホストすることから始めます。
会社のポリシーにより、アプリケーションごとに専用の AWS アカウントを使用する予定です。

これらのアプリケーションは、会社のアプリケーション認証サービスのために、既存のオンプレミス インフラストラクチャへの接続を必要とします。
一部のアプリケーションは、会社が AWS に移行する他のアプリケーションへの接続を必要とします。
20 個のアプリケーションの移行が成功した後、次の移行フェーズでは 1,000 個のアプリケーションが移行されます。

これらの要件を最も少ない管理オーバーヘッドで満たすソリューションはどれですか?
```
![image](https://user-images.githubusercontent.com/60680996/201681079-1ccb5205-5b82-4340-b424-7083a9bde999.png)

![image](https://user-images.githubusercontent.com/60680996/201681169-d1917cfb-9210-47ec-8aa0-bbe51e665420.png)


## No.4
```
A company deployed multiple VPCs in the ap-southeast-1 Region, the us-east-1 Region, and the eu-west-2 Region. 
The company deployed one transit gateway in each Region. 
The company established an AWS Direct Connect connection from an on-premises data center in ap-southeast-1. 
A network engineer configured one Direct Connect gateway. 
The network engineer must complete the configuration of the network path between the on-premises data center and the VPCs in the three Regions.

Which solution will meet these requirements?

ある企業が、ap-southeast-1 リージョン、us-east-1 リージョン、および eu-west-2 リージョンに複数の VPC をデプロイしました。
同社は、各リージョンに 1 つのトランジット ゲートウェイをデプロイしました。
同社は、ap-southeast-1 のオンプレミス データセンターから AWS Direct Connect 接続を確立しました。
ネットワーク エンジニアは、1 つの Direct Connect ゲートウェイを構成しました。
ネットワーク エンジニアは、オンプレミス データセンターと 3 つのリージョンの VPC 間のネットワーク パスの構成を完了する必要があります。

これらの要件を満たすソリューションはどれですか?
```
![image](https://user-images.githubusercontent.com/60680996/201681982-4b1afd23-2d4d-403c-af37-c5c2ed42908f.png)

![image](https://user-images.githubusercontent.com/60680996/201682062-57f8da7b-ad8e-499c-8528-101635881c90.png)


## No.5
```
A network engineer needs to find a solution that prevents DNS traffic from being hijacked. 
The network engineer has configured DNS Security Extensions (DNSSEC) in Amazon Route 53. 
However, the KeySigningKey status is ACTION_NEEDED. This status causes a connectivity outage for the clients that use DNS validating resolvers.

Which action will resolve this issue?


ネットワーク エンジニアは、DNS トラフィックがハイジャックされるのを防ぐソリューションを見つける必要があります。
ネットワークエンジニアは、Amazon Route 53 で DNS Security Extensions (DNSSEC) を設定しました。
ただし、KeySigningKey のステータスは ACTION_NEEDED です。 このステータスにより、DNS 検証リゾルバーを使用するクライアントの接続が停止します。

この問題を解決するアクションはどれですか?
```
![image](https://user-images.githubusercontent.com/60680996/201682566-5f777147-2dfb-4967-87b3-5e5e67f54345.png)

![image](https://user-images.githubusercontent.com/60680996/201682626-8c8686bf-e713-43ef-a723-f2b057b9e08f.png)


## No.6
```
A security engineer pings an Amazon EC2 instance from a home computer (IP address 203.0.113.12) but does not receive a response. 
The security engineer finds the following two records in the VPC flow logs:

2 123456789010 eni-1235b8ca123456789 203.0.113.12 172.31.16.139 0 0 1 4 336 1432917027 1432917142 ACCEPT OK

2 123456789010 eni-1235b8ca123456789 172.31.16.139 203.0.113.12 0 0 1 4 336 1432917094 1432917142 REJECT OK

How can the security engineer determine why the home computer did not receive a response from the EC2 instance?


セキュリティ エンジニアが自宅のコンピューター (IP アドレス 203.0.113.12) から Amazon EC2 インスタンスに ping を送信しますが、応答がありません。
セキュリティ エンジニアは、VPC フロー ログで次の 2 つのレコードを見つけます。

セキュリティ エンジニアは、ホーム コンピューターが EC2 インスタンスから応答を受信しなかった理由をどのように判断できますか?
```
![image](https://user-images.githubusercontent.com/60680996/201683367-efea0c36-3a5c-4150-8bc9-f2af9d6da616.png)


## No.7
```
A financial company wants to establish a private connection between the company's offices and its resources on AWS. The company has many VPCs, Amazon EC2 instances, and databases in the us-east-1 Region and in the us-west-2 Region. A network engineer determines that the company needs 7–8 Gbps of bandwidth between the company's corporate network resources and AWS. The network engineer needs a solution that will provide high resiliency for the company's critical workloads.

Which solution will meet these requirements?

金融会社は、AWS 上の会社のオフィスとそのリソースの間にプライベート接続を確立したいと考えています。 この会社には、us-east-1 リージョンと us-west-2 リージョンに多数の VPC、Amazon EC2 インスタンス、およびデータベースがあります。 ネットワーク エンジニアは、会社の企業ネットワーク リソースと AWS の間に 7 ～ 8 Gbps の帯域幅が必要であると判断しました。 ネットワーク エンジニアは、会社の重要なワークロードに対して高い回復力を提供するソリューションを必要としています。

これらの要件を満たすソリューションはどれですか?
```

![image](https://user-images.githubusercontent.com/60680996/202841913-87d90bfd-2b1e-491d-bb2d-4d589dcd795c.png)

![image](https://user-images.githubusercontent.com/60680996/202841932-d25a985a-10d8-4a5b-a58b-4f65dea14a9e.png)

![image](https://user-images.githubusercontent.com/60680996/202841947-24cacd1e-3f0b-4bf3-b159-cc9a1dfd7a6f.png)



## No.8
```
A network engineer manages two AWS Direct Connect connections between a company's on-premises equipment and AWS. Each connection has a public VIF that the network engineer configured with a private Autonomous System Number (ASN). The network engineer wants to configure active/passive routing between the Direct Connect connections to access AWS public endpoints.

Which combination of BGP configurations must the network engineer apply to the on-premises equipment? (Select TWO.)

ネットワークエンジニアは、企業のオンプレミス機器と AWS の間の 2 つの AWS Direct Connect 接続を管理しています。 各接続には、ネットワーク エンジニアがプライベート自律システム番号 (ASN) を使用して構成したパブリック VIF があります。 ネットワーク エンジニアは、Direct Connect 接続間のアクティブ/パッシブ ルーティングを構成して、AWS パブリック エンドポイントにアクセスしたいと考えています。

ネットワーク エンジニアがオンプレミス機器に適用する必要がある BGP 構成の組み合わせはどれですか? (2 つ選択してください。)
```

![image](https://user-images.githubusercontent.com/60680996/202842041-e9ab67d0-d031-4e67-a2e4-711a952d7926.png)

![image](https://user-images.githubusercontent.com/60680996/202842066-416875af-fbc0-4bf1-bb4d-e67eba8e98fa.png)

→自分の回答があっている気がする。サポートに聞いてみよう。

## No.9
```
A company has two AWS Direct Connect connections in different AWS Regions between two on-premises data centers and two VPCs. An inter-data center connection connects the two data centers. A network engineer configured two private VIFs, VIF A and VIF B. The network engineer configured VIF A for the data center in the us-east-1 Region. The network engineer configured VIF B for the data center in the us-west-2 Region. Both VIFs terminate on a single Direct Connect gateway. The network engineer set up the on-premises routers to prefer VIF A for the production networks and VIF B for the development networks.

The company needs an active/passive solution that sustains the complete loss of a Direct Connect link and ensures the symmetry of flows between the on-premises networks and AWS.

Which configurations will meet these requirements? (Select TWO.)


企業は、2 つのオンプレミス データセンターと 2 つの VPC の間で、異なる AWS リージョンに 2 つの AWS Direct Connect 接続を持っています。 データセンター間接続は、2 つのデータセンターを接続します。 ネットワーク エンジニアは、VIF A と VIF B の 2 つのプライベート VIF を構成しました。ネットワーク エンジニアは、us-east-1 リージョンのデータ センター用に VIF A を構成しました。 ネットワークエンジニアは、us-west-2 リージョンのデータセンター用に VIF B を構成しました。 両方の VIF は、単一の Direct Connect ゲートウェイで終了します。 ネットワーク エンジニアは、運用ネットワークには VIF A を優先し、開発ネットワークには VIF B を優先するようにオンプレミス ルーターを設定しました。

同社は、Direct Connect リンクの完全な損失を維持し、オンプレミス ネットワークと AWS 間のフローの対称性を確保するアクティブ/パッシブ ソリューションを必要としています。

これらの要件を満たす構成はどれですか? (2 つ選択してください。)
```

![image](https://user-images.githubusercontent.com/60680996/202842545-ddb4d5ce-d9bb-4605-ae50-4a94e619c1c9.png)

![image](https://user-images.githubusercontent.com/60680996/202842564-ea70f457-ae61-49ad-80c3-0af1a745c353.png)

![image](https://user-images.githubusercontent.com/60680996/202842582-bdb9d3a1-bfb0-4c9d-995f-7eaf5e493937.png)

## No.10
```
A company recently established an AWS Direct Connect connection from its on-premises data center to AWS. A network engineer blocked all traffic that is destined for Amazon DynamoDB over the company's gateway to the internet at the company's on-premises firewall. DynamoDB traffic must travel across only the Direct Connect connection. Currently, no one in the on-premises data center can access DynamoDB.

Which solution will resolve this connectivity issue?

ある企業は最近、自社のオンプレミス データセンターから AWS への AWS Direct Connect 接続を確立しました。 ネットワークエンジニアは、会社のオンプレミスファイアウォールで、会社のインターネットへのゲートウェイを介して Amazon DynamoDB 宛てのすべてのトラフィックをブロックしました。 DynamoDB トラフィックは、Direct Connect 接続のみを通過する必要があります。 現在、オンプレミス データセンターの誰も DynamoDB にアクセスできません。

この接続の問題を解決するソリューションはどれですか?
```

![image](https://user-images.githubusercontent.com/60680996/202842753-4413528a-205f-43bf-8a64-5f05c001aa9e.png)

![image](https://user-images.githubusercontent.com/60680996/202842768-89c53d7b-4263-4cbd-90fc-3090e0cdcf03.png)


## No.11
```
A company's security team must encrypt all data that leaves the company's on-premises data center at the network layer. Data transmission must use dedicated connections. The security team must also centrally log all traffic flow in Amazon VPC environments. The company orders an AWS Direct Connect connection to build out this design.

Which combination of steps should the company take to ensure that connectivity to AWS meets these security requirements? (Select TWO.)

企業のセキュリティ チームは、ネットワーク層で企業のオンプレミス データ センターを出るすべてのデータを暗号化する必要があります。 データ転送には専用接続を使用する必要があります。 セキュリティ チームは、Amazon VPC 環境のすべてのトラフィック フローを一元的にログに記録する必要もあります。 同社は、この設計を構築するために AWS Direct Connect 接続を注文しました。

AWS への接続がこれらのセキュリティ要件を満たしていることを確認するために、会社はどの手順を組み合わせて実行する必要がありますか? (2 つ選択してください。)
```

![image](https://user-images.githubusercontent.com/60680996/202842902-38a08235-6bd6-4251-a752-6b65a2843eb4.png)

![image](https://user-images.githubusercontent.com/60680996/202842915-dfec5097-e20c-4fc1-b596-48b367d04f68.png)


## No.12
```
A company has a static website. The company updates the website files quarterly. Customers must use secure connections (HTTPS) to connect to the website. The company wants to use its own domain name for the website (www.example.com). The company creates an Amazon S3 bucket to store the website files. The company creates a wildcard certificate in AWS Certificate Manager (ACM) for example.com.

Which solution will meet these requirements?

会社には静的な Web サイトがあります。 同社はウェブサイトのファイルを四半期ごとに更新します。 ウェブサイトへの接続には、安全な接続 (HTTPS) を使用する必要があります。 この会社は、Web サイトに独自のドメイン名 (www.example.com) を使用したいと考えています。 会社は、ウェブサイトのファイルを保存するために Amazon S3 バケットを作成します。 会社は、AWS Certificate Manager (ACM) で example.com のワイルドカード証明書を作成します。

これらの要件を満たすソリューションはどれですか?
```

![image](https://user-images.githubusercontent.com/60680996/202843003-5a604888-8880-4721-922d-48f1ebec50b1.png)

![image](https://user-images.githubusercontent.com/60680996/202843031-6e602a91-cc14-4ccf-bc8f-69c8d94ca0dd.png)


## No.13
```
A company has an AWS Direct Connect connection between an on-premises data center and AWS. To access public AWS endpoints, a network engineer has configured a public VIF. The company configured the on-premises router where the public VIF terminates to perform port address translation (PAT) to the public peer IP address. To know the current list of prefixes that AWS will advertise, the network engineer downloads the AWS IP ranges in JSON format and configures filtering on the on-premises router accordingly.

A maintenance event disrupted connectivity between the on-premises data center and AWS. When the maintenance event ended, BGP peering was up, but connectivity was not re-established.

Which action will restore connectivity?

ある会社は、オンプレミスのデータセンターと AWS の間に AWS Direct Connect 接続を持っています。 パブリック AWS エンドポイントにアクセスするために、ネットワーク エンジニアはパブリック VIF を構成しました。 同社は、パブリック VIF が終了するオンプレミス ルーターを構成して、パブリック ピア IP アドレスへのポート アドレス変換 (PAT) を実行しました。 AWS がアドバタイズするプレフィックスの現在のリストを知るために、ネットワーク エンジニアは AWS の IP 範囲を JSON 形式でダウンロードし、それに応じてオンプレミス ルーターでフィルタリングを構成します。

メンテナンス イベントにより、オンプレミス データセンターと AWS 間の接続が中断されました。 メンテナンス イベントが終了したとき、BGP ピアリングはアップしていましたが、接続は再確立されていませんでした。

接続を復元するアクションはどれですか?
```

![image](https://user-images.githubusercontent.com/60680996/202843157-7bcc1853-ee9c-4518-8607-73f4f1ab5de5.png)

![image](https://user-images.githubusercontent.com/60680996/202843167-f0dcfd0e-9e39-4ba1-8be4-a4250b9c7c2b.png)


## No.14
```
A company deployed an application in several private subnets within a VPC. Each subnet resides in one of the three Availability Zones in an AWS Region. A network engineer must give the application the ability to download updates from the internet. The network engineer deployed a NAT gateway in a single public subnet. The company configured the application with IPv6 addresses. The application needs to connect only to IPv6 update services.

Which combination of steps are necessary to establish the required communication? (Select TWO.)

ある企業が、VPC 内の複数のプライベート サブネットにアプリケーションをデプロイしました。 各サブネットは、AWS リージョンの 3 つのアベイラビリティーゾーンのいずれかに存在します。 ネットワーク エンジニアは、インターネットから更新プログラムをダウンロードする機能をアプリケーションに付与する必要があります。 ネットワーク エンジニアは、単一のパブリック サブネットに NAT ゲートウェイを展開しました。 同社は、IPv6 アドレスを使用してアプリケーションを構成しました。 アプリケーションは、IPv6 更新サービスにのみ接続する必要があります。

必要なコミュニケーションを確立するために必要なステップの組み合わせはどれですか? (2 つ選択してください。)
```

![image](https://user-images.githubusercontent.com/60680996/202843357-69fd1a0e-7b8a-428d-b8a9-4be7ff1179ee.png)

![image](https://user-images.githubusercontent.com/60680996/202843371-18710bea-731b-4743-ae07-b248bf290a2a.png)

![image](https://user-images.githubusercontent.com/60680996/202843378-4ac3d04d-dff7-4bde-8e06-ac289e8ae617.png)


## No.15
```
A company uses AWS to host all its applications. The company has isolated each application in its own VPC. The company also isolates different environments such as development, test, and production in separate VPCs. A network engineer needs to automate the creation of VPCs to enforce the company's network and security standards. Additionally, the company requires the CIDR range for each VPC to be unique.

Which solution will meet these requirements?


企業は AWS を使用してすべてのアプリケーションをホストしています。 同社は、各アプリケーションを独自の VPC に分離しています。 同社はまた、開発、テスト、本番などのさまざまな環境を別々の VPC に分離しています。 ネットワーク エンジニアは、VPC の作成を自動化して、会社のネットワークとセキュリティの標準を適用する必要があります。 さらに、会社は各 VPC の CIDR 範囲を一意にする必要があります。

これらの要件を満たすソリューションはどれですか?
```
![image](https://user-images.githubusercontent.com/60680996/202843483-10703d8e-720e-46ac-8774-ff6718ffad8f.png)

![image](https://user-images.githubusercontent.com/60680996/202843495-cad03ff5-5811-471e-bc85-3245b996cb38.png)
