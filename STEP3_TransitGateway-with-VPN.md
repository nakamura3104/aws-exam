# Transit Gateway + VPN
 - Enable BGP ECMP

## White Paper
https://docs.aws.amazon.com/ja_jp/whitepapers/latest/aws-vpc-connectivity-options/aws-transit-gateway-vpn.html

![fig](https://docs.aws.amazon.com/ja_jp/whitepapers/latest/aws-vpc-connectivity-options/images/image5.png)

## 1. Delete AS-Path Prepend and Weight from VyOS for ECMP
### VyOS Config
```
//Delete Priority for AWS => VyOS
delete protocols bgp neighbor 169.254.61.113 address-family ipv4-unicast route-map export 'VPC-Tunnel-2-OUT'

//Delete Priority for VyOS => AWS
delete protocols bgp neighbor 169.254.234.49 address-family ipv4-unicast weight '300'
```


- 変更後の状態
```
//BGP Table
vyos@vyos:~$ show ip bgp
~~
   Network          Next Hop            Metric LocPrf Weight Path
*> 10.0.2.0/24      0.0.0.0                  0         32768 i
*> 10.99.0.0/16     169.254.234.49         100             0 64512 i
*=                  169.254.61.113         100             0 64512 i

//Main
vyos@vyos:~$ show ip bgp ipv4 unicast neighbors 169.254.234.49 advertised-routes
~~
   Network          Next Hop            Metric LocPrf Weight Path
*> 10.0.2.0/24      0.0.0.0                  0         32768 i
*> 10.99.0.0/16     0.0.0.0                              300 64512 i

Total number of prefixes 2


//Backup
vyos@vyos:~$ show ip bgp ipv4 unicast neighbors 169.254.61.113 advertised-routes
~~
   Network          Next Hop            Metric LocPrf Weight Path
*> 10.0.2.0/24      0.0.0.0                  0         32768 i
*> 10.99.0.0/16     0.0.0.0                              300 64512 i

Total number of prefixes 2
```

## 2. AWS Settings
 - 各サブネットからTGW経由で通信をしたい場合、そのサブネットと同じAZにTGWがアタッチされている必要がある。
 - TGWは専用のサブネットを設けアタッチするのが推奨。
 - TGWで通信したいサブネットは、TGW向けのStaticルートをルートテーブルに登録する必要がある。


```terraform
locals { tgw = true }

//Transit Gateway
resource "aws_ec2_transit_gateway" "tgw" {
  count = local.tgw ? 1 : 0

  auto_accept_shared_attachments = "enable"

  tags = {
    "Name" = "satoshi-tgw"
  }
}

// Transit Gaateway attachment to Network Subnet.
resource "aws_ec2_transit_gateway_vpc_attachment" "tgw" {
  count = local.tgw ? 1 : 0

  subnet_ids = [
    aws_subnet.network.id,
  ]
  transit_gateway_id = aws_ec2_transit_gateway.tgw.0.id
  vpc_id             = var.vpc.id

  tags = {
    "Name" = "tgw-attach-shared-vpc"
  }
}

// Add Route via VyOS for Public subnet
resource "aws_route" "public_tgw" {
  count = local.tgw ? 1 : 0

  route_table_id         = aws_route_table.pub.id
  destination_cidr_block = "10.0.2.0/24"
  transit_gateway_id     = aws_ec2_transit_gateway.tgw.0.id
}

// Attachment with SIte-to-Site VPN
resource "aws_vpn_connection" "vpn" {
  count = local.vpn ? 1 : 0

  #vpn_gateway_id = aws_vpn_gateway.vpn_gw.0.id
  transit_gateway_id = aws_ec2_transit_gateway.tgw.0.id

  customer_gateway_id = aws_customer_gateway.cgw.0.id
  type                = "ipsec.1"
  static_routes_only  = false

  tunnel1_log_options {
    cloudwatch_log_options {
      log_enabled       = true
      log_group_arn     = aws_cloudwatch_log_group.vpn.0.arn
      log_output_format = "json"
    }
  }

  tags = {
    Name = "${var.name}-vpn-01"
  }
}
```

## 3. Test & Confirm
- MED属性は両方とも100で通知（[VGWの時は100と200でBackup側が優先だった](https://github.com/nakamura3104/aws-exam/blob/main/STEP2_Add-Second-VPN.md?plain=1#L62)）

```
vyos@vyos:~$ show ip bgp
~~
   Network          Next Hop            Metric LocPrf Weight Path
*> 10.0.2.0/24      0.0.0.0                  0         32768 i
*> 10.99.0.0/16     169.254.234.49         100             0 64512 i
*                   169.254.61.113         100             0 64512 i
```

## 完成系
![image](https://user-images.githubusercontent.com/60680996/203590744-afa15d7d-5d3a-4fc7-95d2-ab8828965019.png)

- 2.cloud hub
- 3.direct connect gw + transit gw + vpn
- 4.dx-gw + tgw + vpn vpc マルチリージョン
- 5.dx-gw + tgw + vpn vpc クロスアカウント
