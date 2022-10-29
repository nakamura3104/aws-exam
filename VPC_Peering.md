# CloudFormationでのクロスアカウントVPC Peering

## 参考サイト

https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/peer-with-vpc-in-another-account.html

https://dev.classmethod.jp/articles/crossaccount-vpcpeering-cloudformation/

## ポイント
 - アクセプタ側とリクエスタ側でそれぞれCFnテンプレートを作成・デプロイする必要がある。
 - アクセプタ側では、リクエスタ側が接続を承認するためのスイッチロールを作成する必要がある。

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
