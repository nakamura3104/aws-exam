# クロスアカウントVPC Peering (CloudFormation vs Terraform)

## 参考サイト

https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/peer-with-vpc-in-another-account.html

https://dev.classmethod.jp/articles/crossaccount-vpcpeering-cloudformation/

## ポイント
### CloudFormationの場合
 - アクセプタ側AWSアカウントとリクエスタ側AWSアカウントでそれぞれCFnテンプレートを作成・デプロイする必要がある。
 - アクセプタ側では、リクエスタ側が接続を承認するためのスイッチロールを作成する必要がある。

### Terraformの場合
 - リクエスタ側のAWSアカウントのEC2やCloud9よりデプロイする想定で作成。
 - 同じtfファイル内に記述できるがアクセプタ側のAWSアカウントにデプロイするためのクレデンシャル設定は必要。（今回は、スイッチロール用のIAMロールを事前に作成）


## CFn Template

 - アクセプタ側
  
```yml
AWSTemplateFormatVersion: 2010-09-09
Description: Create a VPC and an assumable role for cross account VPC peering.

Parameters:
  PeerRequesterAccountId:
    Type: String
    
    
Resources:
#---------------------------------------------#
# Accepter VPC
#---------------------------------------------#
  vpc:
    Type: 'AWS::EC2::VPC'
    Properties:
      CidrBlock: 10.1.0.0/16
      EnableDnsSupport: false
      EnableDnsHostnames: false
      InstanceTenancy: default
      
#---------------------------------------------#
# SwitchRole used by the requester to accept.
#---------------------------------------------#
  peerRole:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Statement:
          - Principal:
              AWS: !Ref PeerRequesterAccountId
            Action:
              - 'sts:AssumeRole'
            Effect: Allow
      Path: /
      Policies:
        - PolicyName: root
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Action: 'ec2:AcceptVpcPeeringConnection'
                Resource: '*'
Outputs:
  VPCId:
    Value: !Ref vpc
  RoleARN:
    Value: !GetAtt 
      - peerRole
      - Arn
```


 - リクエスタ側

```yml
AWSTemplateFormatVersion: 2010-09-09
Description: Create a VPC and a VPC Peering connection using the PeerRole to accept.

Parameters:
  PeerVPCAccountId:
    Type: String
  PeerVPCId:
    Type: String
  PeerRoleArn:
    Type: String
    
Resources:
#---------------------------------------------#
# Requester VPC
#---------------------------------------------#
  vpc:
    Type: 'AWS::EC2::VPC'
    Properties:
      CidrBlock: 10.2.0.0/16
      EnableDnsSupport: false
      EnableDnsHostnames: false
      InstanceTenancy: default
#---------------------------------------------#
# VPC Peering Connections with SwitchRole for Accept.
#---------------------------------------------#      
  vpcPeeringConnection:
    Type: 'AWS::EC2::VPCPeeringConnection'
    Properties:
      VpcId: !Ref vpc
      PeerVpcId: !Ref PeerVPCId
      PeerOwnerId: !Ref PeerVPCAccountId
      PeerRoleArn: !Ref PeerRoleArn
Outputs:
  VPCId:
    Value: !Ref vpc
  VPCPeeringConnectionId:
    Value: !Ref vpcPeeringConnection
```

## Terraform
 - リクエスタ側のAWSアカウントからデプロイする想定のコードです。
 
```terraform

#---------------------------------------------
# Provider
#---------------------------------------------

# Requester
provider "aws" {
  region = "ap-northeast-1"
}

# Accepter
provider "aws" {
  alias  = "accepter"
  region = "ap-northeast-1"
  assume_role {
    role_arn = "arn:aws:iam::<accepter_aws_accound_id>:role/<role_name>"
  }
}

#---------------------------------------------
# vpc
#---------------------------------------------

# Requester
resource "aws_vpc" "vpc" {
  cidr_block           = "10.1.0.0/16"
  enable_dns_support   = true
  enable_dns_hostnames = true
  instance_tenancy     = "default"
}

# Accepter
resource "aws_vpc" "vpc_accepter" {
  provider = aws.accepter

  cidr_block           = "10.2.0.0/16"
  enable_dns_support   = true
  enable_dns_hostnames = true
  instance_tenancy     = "default"
}

#---------------------------------------------
# Peering
#---------------------------------------------
data "aws_caller_identity" "accepter" {
  provider = aws.accepter
}

# Requester
resource "aws_vpc_peering_connection" "requester" {
  vpc_id        = aws_vpc.vpc.id
  peer_vpc_id   = aws_vpc.vpc_accepter.id
  peer_owner_id = data.aws_caller_identity.accepter.account_id
  peer_region   = "ap-northeast-1"
  auto_accept   = false
}

# Accepter
resource "aws_vpc_peering_connection_accepter" "accepter" {
  provider                  = aws.accepter
  vpc_peering_connection_id = aws_vpc_peering_connection.requester.id
  auto_accept               = true
}
```

## 所感
Peering作成時の承認用にIAMロールを作成する必要があったり、テンプレートファイルの作成とデプロイをアカウント個別に行う必要があるなど、CloudFormationの方がめんどくさいという印象でした。
