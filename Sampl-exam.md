1) あるゲーム会社が、1 つの AWS リージョンでホストされる、世界中で利用できるゲームの発売を計画してい
ます。ゲームバックエンドは、Auto Scaling グループの一部である Amazon EC2 インスタンスでホストされま
す。ゲームでは、ゲームクライアントとバックエンド間の双方向ストリーミングに gRPC プロトコルを使用しま
す。同社は、ゲームを保護するために、送信元 IP アドレスに基づいて着信トラフィックをフィルタリングする
必要があります。

これらの要件を満たすソリューションはどれですか。

- A) Application Load Balancer (ALB) エンドポイントを使用して AWS Global Accelerator アクセラレーターを設定します。ALB を Auto Scaling グループにアタッチします。AWS WAF のウェブ ACL を ALB用に設定して、送信元 IP アドレスに基づいてトラフィックをフィルタリングします。
B) Network Load Balancer (NLB) エンドポイントを使用して AWS Global Accelerator アクセラレーター
を設定します。NLB を Auto Scaling グループにアタッチします。EC2 インスタンスのセキュリティグ
ループを設定して、送信元 IP アドレスに基づいてトラフィックをフィルタリングします。
C) Application Load Balancer (ALB) エンドポイントを使用して Amazon CloudFront ディストリビューシ
ョンを設定します。ALB を Auto Scaling グループにアタッチします。AWS WAF のウェブ ACL を ALB
用に設定して、送信元 IP アドレスに基づいてトラフィックをフィルタリングします。
D) Network Load Balancer (NLB) エンドポイントを使用して Amazon CloudFront ディストリビューション
を設定します。NLB を Auto Scaling グループにアタッチします。EC2 インスタンスのセキュリティグ
ループを設定して、送信元 IP アドレスに基づいてトラフィックをフィルタリングします。
