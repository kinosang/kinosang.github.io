---
title: Ubuntu 18.04 安裝設定 IKEv2
categories:
  - 網際網路
date: 2019-06-02 23:04:09
tags:
---

前幾天需要在公司辦公室設定 IKEv2 VPN, 於是在伺服器上建立了一個 Ubuntu 18.04 LXC 容器, 分配 IP 地址 `10.0.0.3`.

因伺服器在辦公室路徑器和防火牆後面, 所以需要先在防火牆上開放 `500/UDP`, `4500/UDP` 和 `ESP (Protocol 50)`, 並在路徑器上將 `500/UDP`, `4500/UDP` 和 `ESP` 轉發給 `10.0.0.3`.

<!--more-->

### 安裝 StrongSwan

```bash
$ sudo apt update
$ sudo apt install strongswan libcharon-extra-plugins # strongswan-plugin-dhcp 已被 libcharon-extra-plugins 取代
```

### 設定 StrongSwan

需要將 `server-cert.crt` 放入 `/etc/ipsec.d/certs/`, 將 `server-key.pem` 放入 `/etc/ipsec.d/private/`, 將 CA 放入 `/etc/ipsec.d/cacerts/`.

*注意：server-cert.crt 需要具備 Digital Signature, Key Encipherment, serverAuth 和 ikeIntermediate 屬性*

```bash
$ sudo vim /etc/ipsec.conf
```

```nginx
config setup
    charondebug="ike 2, knl 2, cfg 2, net 2, esp 2, dmn 2, mgr 2"
    uniqueids=no

conn %default
    keyexchange=ikev2

    ike=aes128-sha1-modp1024,aes128-sha1-modp1536,aes128-sha1-modp2048,aes128-sha256-ecp256,aes128-sha256-modp1024,aes128-sha256-modp1536,aes128-sha256-modp2048,aes256-aes128-sha256-sha1-modp2048-modp4096-modp1024,aes256-sha1-modp1024,aes256-sha256-modp1024,aes256-sha256-modp1536,aes256-sha256-modp2048,aes256-sha256-modp4096,aes256-sha384-ecp384,aes256-sha384-modp1024,aes256-sha384-modp1536,aes256-sha384-modp2048,aes256-sha384-modp4096,aes256gcm16-aes256gcm12-aes128gcm16-aes128gcm12-sha256-sha1-modp2048-modp4096-modp1024,3des-sha1-modp1024!
    esp=aes128-aes256-sha1-sha256-modp2048-modp4096-modp1024,aes128-sha1,aes128-sha1-modp1024,aes128-sha1-modp1536,aes128-sha1-modp2048,aes128-sha256,aes128-sha256-ecp256,aes128-sha256-modp1024,aes128-sha256-modp1536,aes128-sha256-modp2048,aes128gcm12-aes128gcm16-aes256gcm12-aes256gcm16-modp2048-modp4096-modp1024,aes128gcm16,aes128gcm16-ecp256,aes256-sha1,aes256-sha256,aes256-sha256-modp1024,aes256-sha256-modp1536,aes256-sha256-modp2048,aes256-sha256-modp4096,aes256-sha384,aes256-sha384-ecp384,aes256-sha384-modp1024,aes256-sha384-modp1536,aes256-sha384-modp2048,aes256-sha384-modp4096,aes256gcm16,aes256gcm16-ecp384,3des-sha1!

    dpdaction=clear
    dpddelay=300s
    rekey=no

    fragmentation=yes
    forceencaps=yes

    left=%any
    leftid=@<your-server-hostname>
    leftcert=server-cert.pem
    leftsendcert=always
    leftsubnet=10.0.0.0/24 # NAT
    # leftsubnet=0.0.0.0/0
    leftfirewall=yes

    right=%any
    rightid=%any
    rightauth=eap-mschapv2
    rightsourceip=10.0.1.0/24
    # rightsourceip=%dhcp
    rightsubnet=10.0.1.0/24
    rightdns=10.0.0.1
    rightsendcert=never

    compress=no
    type=tunnel

conn IPSec-IKEv2
    keyexchange=ikev2
    auto=add

    eap_identity=%any
```

```bash
$ sudo vim /etc/ipsec.secrets
```

```nginx
...
: RSA "server-key.pem"
vpn : EAP "vpn" # 使用者名稱和密碼
...
```

### 啟動 StrongSwan

```bash
$ sudo systemctl start strongswan
$ sudo systemctl enable strongswan
```

### 設定轉發

```bash
$ sudo ufw allow from 10.0.0.0/24 to any port 22 # 只允許辦公室訪問 SSH
$ sudo ufw allow 500,4500/udp # 允許 ISAKMP, NAT-T
```

```bash
$ sudo vim /etc/ufw/sysctl.conf
```

```nginx
...
net/ipv4/ip_forward=1
...
net/ipv4/conf/all/accept_redirects=0
...
net/ipv4/conf/all/send_redirects=0
...
net/ipv4/ip_no_pmtu_disc=1
...
```

```bash
$ sudo vim /etc/ufw/before.rules
```

```nginx
#
# rules.before
#
# Rules that should be run before the ufw command line added rules. Custom
# rules should be added to one of these chains:
#   ufw-before-input
#   ufw-before-output
#   ufw-before-forward
#

# 新增 nat 和 mangle

*nat
-A POSTROUTING -s 10.0.1.0/24 -o eth0 -m policy --pol ipsec --dir out -j ACCEPT
-A POSTROUTING -s 10.0.1.0/24 -o eth0 -j MASQUERADE
COMMIT

*mangle
-A FORWARD --match policy --pol ipsec --dir in -s 10.0.1.0/24 -o eth0 -p tcp -m tcp --tcp-flags SYN,RST SYN -m tcpmss --mss 1361:1536 -j TCPMSS --set-mss 1360
COMMIT
...
*filter
...
# 新增 filter 策略
-A ufw-before-forward --match policy --pol ipsec --dir in --proto esp -s 10.0.1.0/24 -j ACCEPT
-A ufw-before-forward --match policy --pol ipsec --dir out --proto esp -d 10.0.1.0/24 -j ACCEPT
...
```

```bash
$ sudo ufw enable
```

### DNS 問題

因為前文設定 `leftsubnet=10.0.0.0/24`, 在 iOS 和 macOS 上 `rightdns=10.0.0.1` 不起作用,
所以需要通過 `attr` 外掛來使內網域名正常解析.

*`leftsubnet=0.0.0.0/0` 時所有網路通訊都會通過 VPN*

```bash
$ sudo vim /etc/ipsec.conf
```

```nginx
...
    # check out /etc/strongswan.d/charon/attr.conf
    # rightdns=10.0.0.1
...
```

```bash
$ sudo vim /etc/strongswan.d/charon/attr.conf
```

```nginx
# Section to specify arbitrary attributes that are assigned to a peer via
# configuration payload (CP).
attr {

    # <attr> is an attribute name or an integer, values can be an IP address,
    # subnet or arbitrary value.
    # <attr> =

    # https://www.iana.org/assignments/ikev2-parameters/ikev2-parameters.xhtml#ikev2-parameters-21
    # INTERNAL_DNS_DOMAIN
    25 = joy

    # internal dns server
    dns = 10.0.0.1

    # Whether to load the plugin. Can also be an integer to increase the
    # priority of this plugin.
    load = yes

}
```
