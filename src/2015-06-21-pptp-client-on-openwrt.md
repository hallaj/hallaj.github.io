---
title: PPTP Client on OpenWRT
date: 2015-06-21
tags:
  - pptp
  - openwrt
---

#### PPTP Client on OpenWRT

1. Refresh the OpenWRT's repository

    ```shell
    opkg update
    ```
1. PPTP client installation

    ```shell
    opkg install ppp-mod-pptp kmod-pptp kmod-nf-nathelper-extra
    reboot
    ```
1. OpenWRT network configuration

    ```shell
    config interface 'PPTP_VPN'
            option proto        'pptp'
            option server       'pptp.server'
            option username     'myusername'
            option password     'mypassword'
            option defaultroute '0'  # not using as default route
            option peerdns      '0'  # not using the dns servers
    ```
1. (Optional) Adding static routes for connection via your VPN

    ```shell
    config route
            option interface 'PPTP_VPN'
            option target    '10.10.10.0/24'  # LAN / WAN to connect via PPTP
            option gateway   '172.16.0.1'     # gateway from PPTP
    ```
