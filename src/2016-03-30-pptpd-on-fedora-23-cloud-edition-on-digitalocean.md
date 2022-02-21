---
title: pptpd on Fedora 23 Cloud Edition on DigitalOcean
date: 2016-03-30
tags:
  - pptp
  - openwrt
  - digitalocean
---

Well, I finally did it. I was bored and decided to setup PPTPD on my DigitalOcean's VM and let my OpenWRT router connect to it. This allows me to tunnel using DigitalOcean and enjoy a slightly better international bandwidth via it.

The current downfall that I see is that I had to drop my MTU to 1000 in order to get my speed optimized. I'll still be fiddling around with it to see what works best, but below are the steps done in order to archive it.

#### PPTPD setup on Fedora 23 Cloud Edition, on DigitalOcean

1. Spin off a new DigitalOcean node, and pick Fedora 23.
1. Start with installing PPTPD.

    ```shell
    dnf -y update ; dnf -y install pptpd
    ```
1. Install kernel modules, to include ppp modules, etc

    ```shell
    dnf -y install kernel-modules
    ```
1. Edit the `/etc/ppp/chap-secrets` file, and add your user credentials.
   Since this file contains plain-text password, the permission is set (by default) to 600,
   with `root` user and `root` group ownership.

    ```shell
    # username service password ip_address
    hallaj pptpd password *
    ```
1. Edit `/etc/ppp/options.pptpd` and add the following changes.

    ```shell
    name pptpd  # this needs to match the service part in /etc/ppp/chap-secrets
    mtu  1000   # so far this has given me the best bandwidth setting when I tunnel
    ```
1. Edit `/etc/pptpd.conf` and add the following changes.

    ```shell
    localip 192.168.100.1
    remoteip 192.168.100.200-250
    ```
1. Allow the incoming connections to PPTPD
    ```shell
    iptables -I INPUT -p gre -j ACCEPT
    iptables -I INPUT -p tcp -m tcp --dport 1723 -j ACCEPT
    ```
1. Start up the service, and we're good to go :)

    ```shell
    systemctl start pptpd
    ```
1. (Optional) Enable the service to start on boot-time

    ```shell
    systemctl enable pptpd
    ```
1. (Optional) Save the firewall settings

    ```shell
    service iptables save
    ```

In order to use the internet from the recently created PPTPD, continue ahead.

#### Allowing PPTP clients to use the internet connection

1. Enable IP forwarding from the Fedora server

    ```shell
    sysctl -w net.ipv4.ip_forward=1
    ```

    or to make the changes survive a reboot..

    ```shell
    echo "net.ipv4.ip_forward = 1" > /etc/sysctl.d/51-ip-forwarding.conf
    ```
1. Add nat rules to allow connections to go through

    ```shell
    iptables -I FORWARD -i eth0 -j ACCEPT
    iptables -I FORWARD -i ppp+ -o eth0 -j ACCEPT
    iptables -I FORWARD -i eth0 -o ppp+ -j ACCEPT
    ```
1. (Optional) Save the firewall settings

    ```shell
    service iptables save
    ```
