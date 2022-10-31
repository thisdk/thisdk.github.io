---
title: OpenWrt 路由器配置
tags: OpenWrt
categories: 笔记
date: 2022-10-31 21:00:00
---

### 正常软件包安装
```
opkg install luci-theme-argon luci-i18n-argon-config-zh-cn luci-i18n-vlmcsd-zh-cn luci-i18n-upnp-zh-cn 
opkg install luci-i18n-diskman-zh-cn kmod-usb3 kmod-usb2 luci-proto-ipv6 odhcpd-ipv6only odhcp6c ipv6helper
```

### 摄像头相关
```
opkg install kmod-usb-ehci kmod-usb-ohci kmod-video-uvc luci-i18n-mjpg-streamer-zh-cn
opkg install mjpg-streamer-www mjpg-streamer-input-uvc mjpg-streamer-output-http
```

### 摄像头 luci 源码修改

> /etc/init.d/mjpg-streamer

```
local no_dynctrl
config_get_bool no_dynctrl "$s" 'no_dynctrl' 0
[ "$no_dynctrl" -ne 0 ] && input_arg="${input_arg} --no_dynctrl"
```

### 命令行设置拨号
```
uci set network.wan.proto=pppoe
uci set network.wan.username='13148850427@139.gd'
uci set network.wan.password='850427'
uci commit network
ifup wan
```