# Overview
- DX-GWでDirectConnectを複数リージョンで共用する構成。
- 各リージョン内は、VPC間通信を想定しTransit Gatewayで接続する。

# Diagram
- 2.cloud hub
- 3.direct connect gw + transit gw + vpn
- 4.dx-gw + tgw + vpn vpc マルチリージョン
- 5.dx-gw + tgw + vpn vpc クロスアカウント
