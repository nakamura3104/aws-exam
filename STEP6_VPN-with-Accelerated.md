
# Additional AWS Resource

> Gateway Attachement is MUST be TGW. (Not allowed to use VGW)

```terraform
/* ---------------------------------
 Site-to-Site VPN with Accelerater
----------------------------------- */
resource "aws_vpn_connection" "accelerated_vpn" {
  count      = local.accelerated_vpn ? 1 : 0
  depends_on = [aws_ec2_transit_gateway.tgw.0]

  transit_gateway_id = aws_ec2_transit_gateway.tgw.0.id
  enable_acceleration = true

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
    Name = "${var.name}-vpn-02_accelerated"
  }
}

```
# VyOS Configration

```
// Additional VTI
set interfaces vti vti10 address '169.254.100.22/30'
set interfaces vti vti10 description 'VPC tunnel 2-1'
set interfaces vti vti10 mtu '1436'
set interfaces vti vti11 address '169.254.158.74/30'
set interfaces vti vti11 description 'VPC tunnel 2-2'
set interfaces vti vti11 mtu '1436'

// Additional IPSec (Main)
set vpn ipsec site-to-site peer AWS2 authentication local-id '110.134.213.239'
set vpn ipsec site-to-site peer AWS2 authentication mode 'pre-shared-secret'
set vpn ipsec site-to-site peer AWS2 authentication pre-shared-secret 'F4a9TuaMFWInpI88a8e8Xzh57RqfV4Xd'
set vpn ipsec site-to-site peer AWS2 authentication remote-id '3.33.236.101'
set vpn ipsec site-to-site peer AWS2 description 'VPC tunnel 2-1'
set vpn ipsec site-to-site peer AWS2 ike-group 'AWS'
set vpn ipsec site-to-site peer AWS2 local-address '10.0.2.15'
set vpn ipsec site-to-site peer AWS2 remote-address '3.33.236.101'
set vpn ipsec site-to-site peer AWS2 vti bind 'vti10'
set vpn ipsec site-to-site peer AWS2 vti esp-group 'AWS'

// Additional IPSec (Backup)
set vpn ipsec site-to-site peer AWS2_backup authentication local-id '110.134.213.239'
set vpn ipsec site-to-site peer AWS2_backup authentication mode 'pre-shared-secret'
set vpn ipsec site-to-site peer AWS2_backup authentication pre-shared-secret '_7I5yQtQInFwJv0hM4NdgP9VP1sEQhaB'
set vpn ipsec site-to-site peer AWS2_backup authentication remote-id '52.223.19.127'
set vpn ipsec site-to-site peer AWS2_backup description 'VPC tunnel 2-2'
set vpn ipsec site-to-site peer AWS2_backup ike-group 'AWS'
set vpn ipsec site-to-site peer AWS2_backup local-address '10.0.2.15'
set vpn ipsec site-to-site peer AWS2_backup remote-address '52.223.19.127'
set vpn ipsec site-to-site peer AWS2_backup vti bind 'vti11'
set vpn ipsec site-to-site peer AWS2_backup vti esp-group 'AWS'

// BGP Config
set protocols bgp neighbor 169.254.100.21 address-family ipv4-unicast route-map
set protocols bgp neighbor 169.254.100.21 address-family ipv4-unicast soft-reconfiguration inbound
set protocols bgp neighbor 169.254.100.21 remote-as '65100'
set protocols bgp neighbor 169.254.100.21 timers holdtime '30'
set protocols bgp neighbor 169.254.100.21 timers keepalive '10'
set protocols bgp neighbor 169.254.158.73 address-family ipv4-unicast soft-reconfiguration inbound
set protocols bgp neighbor 169.254.158.73 remote-as '65100'
set protocols bgp neighbor 169.254.158.73 timers holdtime '30'
set protocols bgp neighbor 169.254.158.73 timers keepalive '10'
```


# Ping

- Use NOT Accelerated VPN

```
64 bytes from 10.0.2.15: icmp_seq=309 ttl=63 time=26.5 ms
64 bytes from 10.0.2.15: icmp_seq=310 ttl=63 time=25.4 ms
64 bytes from 10.0.2.15: icmp_seq=311 ttl=63 time=21.0 ms
^C
--- 10.0.2.15 ping statistics ---
311 packets transmitted, 310 received, 0% packet loss, time 31207ms
rtt min/avg/max/mdev = 16.690/30.509/426.705/36.310 ms, pipe 5
```

- Use Accelerated VPN

```
64 bytes from 10.0.2.15: icmp_seq=796 ttl=63 time=26.6 ms
64 bytes from 10.0.2.15: icmp_seq=797 ttl=63 time=23.4 ms
64 bytes from 10.0.2.15: icmp_seq=798 ttl=63 time=34.6 ms
64 bytes from 10.0.2.15: icmp_seq=799 ttl=63 time=24.0 ms
^C
--- 10.0.2.15 ping statistics ---
799 packets transmitted, 798 received, 0% packet loss, time 80263ms
rtt min/avg/max/mdev = 16.371/28.821/128.358/12.426 ms, pipe 2
```
