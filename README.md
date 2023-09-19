# manuals:
```text
https://github.com/hiddify/hiddify-config/discussions/493
https://github.com/woodlyer/gostExample
https://github.com/go-gost/gost
```
## Install:
```text
snap install gost
اجرا شدن به عنوان سرویس:
which gost
آخر فایل راهنمای زیر:
https://github.com/woodlyer/gostExample
بهبود بیشتر:
https://freshman.tech/snippets/linux/auto-restart-systemd-service/
```
## As Service:
### On DE:
```text
on DE:
file: /lib/systemd/system/gost.service

[Unit]
Description=gost
StartLimitIntervalSec=300
StartLimitBurst=10
After=network-online.target
Wants=network-online.target systemd-networkd-wait-online.service

[Service]
Type=simple
User=root
Restart=on-failure
RestartSec=2s
#DynamicUser=true
ExecStart=/snap/bin/gost -L dtls://:8899/:4422
#ExecStart=/usr/bin/gost -C /etc/gost/config.json

[Install]
WantedBy=multi-user.target
```
### On IR:
```text
file1: /lib/systemd/system/gost.sh

#!/bin/bash
#while true
#do
 /snap/gost/539/bin/gost -L tcp://A.A.A.A:9040 -F forward+dtls://B.B.B.B:8899
# sleep 10
#done

file2: /lib/systemd/system/gost.service

[Unit]
Description=gost
StartLimitIntervalSec=300
StartLimitBurst=10
After=network-online.target
Wants=network-online.target systemd-networkd-wait-online.service

[Service]
Type=simple
User=root
Restart=on-failure
RestartSec=2s
#DynamicUser=true
ExecStart=/lib/systemd/system/gost.sh

#ExecStart=/snap/bin/gost -L tcp://A.A.A.A:9040 -F forward+dtls://B.B.B.B:8899
#StandardOutput=syslog
#StandardError=syslog
#ExecStart=/usr/bin/gost -C /etc/gost/config.json
#SyslogIdentifier=%n

[Install]
WantedBy=multi-user.target
```
# Tests:
```text
yes
gost -L kcp://:8899/:4445
gost -L tcp://:9990  -F forward+kcp://B.B.B.B:8899

yes
gost -L wss://:8899/:4445
gost -L tcp://:9990  -F forward+wss://B.B.B.B:8899

yes
gost -L mwss://:8899/:4445
gost -L tcp://:9990  -F forward+mwss://B.B.B.B:8899


yes for shadowsocks-libev
gost -L tls://:8899/:4445
gost -L tcp://:9990  -F forward+tls://B.B.B.B:8899

yes for shadowsocks-libev
gost -L dtls://:8899/:4445
gost -L tcp://:9990  -F forward+dtls://B.B.B.B:8899

no
gost -L tls://:8899/:4445
gost -L tcp://:9990  -F relay+tls://B.B.B.B:8899

no
gost -L dtls://:8899/:4445
gost -L tcp://:9990  -F relay+dtls://B.B.B.B:8899

???
echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all
gost -L icmp://:0
gost -L :9990 -F "relay+icmp://B.B.B.B:12345?keepAlive=true&ttl=10s"


proxy mode:

ok
gost -L=kcp://:8899
gost -L=:9990 -F=kcp://B.B.B.B:8899

ok
gost -L tls://:8899
gost -L=:9990 -F tls://B.B.B.B:8899

ok
gost -L mtls://:8899
gost -L=:9990 -F mtls://B.B.B.B:8899

no
gost -L=kcp://:8899?tcp=true
gost -L=:9990 -F=kcp://B.B.B.B:8899?tcp=true

gost remote port forward
ok but too slow

# server (iran) A.A.A.A
gost -L kcp://:8899

# client (DE)
gost -L=rtcp://:9990/127.0.0.1:4445  -F=kcp://A.A.A.A:8899

# ssh cmd
ssh root@server_ip -p 2222
```


# GO Simple Tunnel

### GO语言实现的安全隧道

[English README](README_en.md)

## 功能特性

- [x] [多端口监听](https://gost.run/getting-started/quick-start/)
- [x] [多级转发链](https://gost.run/concepts/chain/)
- [x] [多协议支持](https://gost.run/tutorials/protocols/overview/)
- [x] [TCP/UDP端口转发](https://gost.run/tutorials/port-forwarding/)
- [x] [反向代理](https://gost.run/tutorials/reverse-proxy/)和[隧道](https://gost.run/tutorials/reverse-proxy-advanced/)
- [x] [TCP/UDP透明代理](https://gost.run/tutorials/redirect/)
- [x] DNS[解析](https://gost.run/concepts/resolver/)和[代理](https://gost.run/tutorials/dns/)
- [x] [TUN/TAP设备](https://gost.run/tutorials/tuntap/)
- [x] [Unix域套接字重定向](https://gost.run/tutorials/unix/)
- [x] [串口重定向](https://gost.run/tutorials/serial/)
- [x] [负载均衡](https://gost.run/concepts/selector/)
- [x] [路由控制](https://gost.run/concepts/bypass/)
- [x] [准入控制](https://gost.run/concepts/admission/)
- [x] [限速限流](https://gost.run/concepts/limiter/)
- [x] [插件系统](https://gost.run/concepts/plugin/)
- [x] [Prometheus监控指标](https://gost.run/tutorials/metrics/)
- [x] [动态配置](https://gost.run/tutorials/api/config/)
- [x] [Web API](https://gost.run/tutorials/api/overview/)
- [ ] Web UI

Wiki站点：[https://gost.run](https://gost.run)

Telegram讨论群：[https://t.me/gogost](https://t.me/gogost)

Google讨论组：[https://groups.google.com/d/forum/go-gost](https://groups.google.com/d/forum/go-gost)

旧版入口：[v2.gost.run](https://v2.gost.run)

## 下载安装


### 二进制文件

[https://github.com/go-gost/gost/releases](https://github.com/go-gost/gost/releases)

### 安装脚本


```bash
# 安装最新版本 [https://github.com/go-gost/gost/releases](https://github.com/go-gost/gost/releases)
bash <(curl -fsSL https://github.com/go-gost/gost/raw/master/install.sh) --install
```
```bash
# 选择要安装的版本
bash <(curl -fsSL https://github.com/go-gost/gost/raw/master/install.sh)
```


### 源码编译

```
git clone https://github.com/go-gost/gost.git
cd gost/cmd/gost
go build
```

### Docker

```
docker run --rm gogost/gost -V
```

### Shadowsocks Android插件

[xausky/ShadowsocksGostPlugin](https://github.com/xausky/ShadowsocksGostPlugin)
