---
layout: post
title:  "How to connect wireless manully in Linux"
date:   2017-02-15
excerpt: "详细介绍如何手动连接无线网络"
tag:
- Linux 
- Network
comments: true
---

无线网的连接在linux中总是会出现这样那样的问题（个人经历是这样的）
尤其是学校的校园网，掉的真是恶心，一掉就再也连不上
我曾经试过NetworkManager，connman，netctl等自动连接工具，但效果不佳
非常无奈地我只能去学手动连接，发现居然十分稳定，只要连上去之后根本不会掉线

一下是手动连接的步骤

## 0. 配置准备

### 0.1 我们所需要的工具有

1. iw
2. ip
3. wpa_supplicant
4. dhcpcd

这几个工具请读者自行安装，不同发行版软件包有所不同，我这里不详细阐述

### 0.2 关闭所有自动连接服务
这里必须强调一下**自动连接服务的运行将导致手动连接无法起作用**
另一方面，我也說一下，不同的自动连接服务同时打开从而导致无法自动连接也是常见的问题
所以如果读者决定以后要尝试手动连接的话，请先把自动连接服务关闭

#### 关闭的方法
首先介绍一下管理服务的进程

> Linux系统中，init有以下三种主要的实现版本:
> 1. System V init:传统的顺序init(Sys V, 读作"sys-five")，为Red Hat Enterprise Linux 和其他发行版使用
> 2. systemd 新出现的init。很多Linux发行版已经或者正在计划转向 systemd。
> 3. Ubuntu 上的init。不过在本书编写是，Ubuntu 也计划转向systemd

（选自《精通LInux》)<br/>
主要就是以上三个，那我就以systemd介绍一下怎么关闭*（会的读者可以自行跳过*<br/>

+ systemctl status` 查看所有的服务
+ sytemctl stop <Tab>` 可以查看正在运行的服务(我这里用的是zsh，不知道bash可不可以
+ systemctl start/stop 服务名` 开启/关闭 该服务
+ systemctl enable/disable 服务名`   开机 自启/禁自启 该服务

至于哪些是自动连接服务，还请读者自行判断（因为太多了……

## 1 获取相关信息

+ 首先获取接口名
`iw dev`
在interface后的就是你的主机借口名，我这里是wlp4s0
+ 激活内核接口
`iw dev set wlp4s0 up`
+ 获取接口状态
`iw dev show wlp4s0`

>         3: wlp4s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group defalt qlen 1000 link/ether b8:86:87:da:68:ba brd ff:ff:ff:ff:ff:ff

其中 `<BROADCAST,MULTICAST,UP,LOWER_UP>`中的 `UP`显示借口已经打开

+ 查看接入点
`iw dev wlp4s0 scan | less` <br/>
需要关注的信息：

+ ** SSID: ** 网络的名称
+ ** Signal: ** 用 dbm (-100 to 0) 报告的无线信号强度。数值越接近零，信号越好。
+ ** Security: **   没有直接报告, 检查 capability 开头的行，如果有 Privacy 信息，例如 capability: ESS Privacy ShortSlotTime (0x0411), 表示网络具有某种程度的保护.

> 如果有 RSN 信息，网络被 Robust Security Network(WPA2) 协议保护。
> 如果有 WPA 信息，网络被 Wi-Fi Protected Access 协议保护。
> 在 RSN 和 WPA 信息块中，可能看到如下信息：
        Group cipher: 数值包括 TKIP, CCMP, both, others.
        Pairwise ciphers: 数值包括 TKIP, CCMP, both, others. 可能和 Group cipher 数值不同.
        Authentication suites: 数值包括 PSK, 802.1x, others. 家用路由器通常可以看到 PSK (i.e. 密码). 在大学中，通常会链接到需要登录名和密码的 802.1x 网络。需要知道其使用的密码管理方式(例如 EAP), 封装方法 (例如 PEAP). 详情请参考 这里 和 这里.        如果没有看到         RSN 或 WPA，但是看到了 Privacy, 表示使用的是 WEP.

(选自archwiki)

## 2. 关联

假设我要连接的wifi ssid为 myssid

+ 无加密
`iw wlp4s0 connect myssid`
+ WEP
使用十六进制或 ASCII 密码(格式是自动识别出来的，因为 WEP 密码长度是固定的): 
`iw dev wlan0 connect your_essid key 0:your_key` <br/>
使用十六进制或 ASCII 密码，第三个是默认 (从0计数，共四个): 
`iw dev wlan0 connect your_essid key d:2:your_key`
+ WPA/WPA2
`wpa_supplicant -B -i wlp4s0 -c <(wpa_passphrase your_SSID your_key)` <br/>
最后可以通过 `iw dev wlp4s0 link`确认是否已经连接成功

## 3. 获取ip
这里我使用dhcp
`dhcpcd wlp4s0`<br/>
其实还可以使用静态ip分配，但是我没用过，这里是archwiki的写法<br/>
`ip addr add 192.168.0.2/24 dev wlp4s0`<br/>
`ip route add default via 192.168.0.1`<br/>

---

我以学校校园网为例，简单示范一下，其实只需要三行命令

+ `ip link set wlp4s0`
+ `iw wlp4s0 connect WUST_Wireless`
+ `dhcpcd wlp4s0`
最后再随便开个网页登陆即可<br/>

最后，假如这样连接又断了怎么办呢，说实话，我用到现在还没有断过，如果真的断了，那就先kill进程，再同样的方法重新连接就好了。