### 1) 
> あるゲーム会社が、1 つの AWS リージョンでホストされる、世界中で利用できるゲームの発売を計画しています。ゲームバックエンドは、Auto Scaling グループの一部である Amazon EC2 インスタンスでホストされます。ゲームでは、ゲームクライアントとバックエンド間の双方向ストリーミングに gRPC プロトコルを使用します。同社は、ゲームを保護するために、送信元 IP アドレスに基づいて着信トラフィックをフィルタリングする必要があります。これらの要件を満たすソリューションはどれですか。

- A) Application Load Balancer (ALB) エンドポイントを使用して AWS Global Accelerator アクセラレーターを設定します。ALB を Auto Scaling グループにアタッチします。AWS WAF のウェブ ACL を ALB用に設定して、送信元 IP アドレスに基づいてトラフィックをフィルタリングします。
- B) Network Load Balancer (NLB) エンドポイントを使用して AWS Global Accelerator アクセラレーターを設定します。NLB を Auto Scaling グループにアタッチします。EC2 インスタンスのセキュリティグループを設定して、送信元 IP アドレスに基づいてトラフィックをフィルタリングします。
- C) Application Load Balancer (ALB) エンドポイントを使用して Amazon CloudFront ディストリビューションを設定します。ALB を Auto Scaling グループにアタッチします。AWS WAF のウェブ ACL を ALB用に設定して、送信元 IP アドレスに基づいてトラフィックをフィルタリングします。
- D) Network Load Balancer (NLB) エンドポイントを使用して Amazon CloudFront ディストリビューションを設定します。NLB を Auto Scaling グループにアタッチします。EC2 インスタンスのセキュリティグループを設定して、送信元 IP アドレスに基づいてトラフィックをフィルタリングします。

### 2) OK 
### 3) NG 
### 4) NG
> S3のエンドポイントの種類、オンプレからのアクセスについて理解する。

### 5) OK
> Transit Gatway Appliance mode が何かわかっていない

### 6) NG
意味不明
### 7) NG
### 8) OK
### 9) OK
### 10) NG

