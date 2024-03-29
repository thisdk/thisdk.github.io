---
title: 服务器Docker容器配置
tags: Linux
categories: 笔记
date: 2022-10-08 13:24:00
---

### Docker 接口创建与卷创建

``` shell
docker network create --driver bridge private
docker volume create portainer
docker volume create redis
```

### Docker 容器创建

``` shell
docker run -d --restart=always --network private --name watchtower -v /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --cleanup
docker run -d --restart=always --network private --name xray -p 443:443 -v /root/xray:/etc/xray teddysun/xray
docker run -d --restart=always --network private --name portainer -v /var/run/docker.sock:/var/run/docker.sock -v portainer:/data portainer/portainer-ce
docker run -d --restart=always --network private --name freenom -v /root/freenom:/conf -v /root/freenom/logs:/app/logs luolongfei/freenom
docker run -d --restart=always --network private --name frps -p 8088:8088 -p 8088:8088/udp -v /root/frp/frps.ini:/etc/frp/frps.ini snowdreamtech/frps
docker run -d --restart=always --network private --name docker.2048 alexwhen/docker-2048
docker run -d --restart=always --network private --name hysteria -p 443:443/udp -v /root/hysteria:/etc/hysteria teddysun/hysteria
docker run -d --restart=always --network private --name redis -v redis:/data redis
docker run -d --restart=always --network private --name tomcat -v /root/tomcat/webapps:/usr/local/tomcat/webapps -v /root/tomcat/logs:/usr/local/tomcat/logs tomcat:9.0-jre11
```

### Acme.sh 证书申请

``` shell
acme.sh --issue -d www.thisdk.tk --keylength ec-256 --standalone
acme.sh --issue -d frp.thisdk.tk --keylength ec-256 --standalone
acme.sh --issue -d 2048.thisdk.tk --keylength ec-256 --standalone
acme.sh --issue -d tomcat.thisdk.tk --keylength ec-256 --standalone
```

### Acme.sh 证书安装

``` shell
acme.sh --install-cert -d www.thisdk.tk --ecc --key-file /root/xray/tls/www.thisdk.tk.key --fullchain-file /root/xray/tls/www.thisdk.tk.cer --reloadcmd "docker restart xray"
acme.sh --install-cert -d frp.thisdk.tk --ecc --key-file /root/xray/tls/frp.thisdk.tk.key --fullchain-file /root/xray/tls/frp.thisdk.tk.cer --reloadcmd "docker restart xray"
acme.sh --install-cert -d 2048.thisdk.tk --ecc --key-file /root/xray/tls/2048.thisdk.tk.key --fullchain-file /root/xray/tls/2048.thisdk.tk.cer --reloadcmd "docker restart xray"
acme.sh --install-cert -d tomcat.thisdk.tk --ecc --key-file /root/xray/tls/tomcat.thisdk.tk.key --fullchain-file /root/xray/tls/tomcat.thisdk.tk.cer --reloadcmd "docker restart xray"
```


### Acme.sh 证书续期

``` shell
acme.sh --renew -d www.thisdk.tk --ecc --force
acme.sh --renew -d frp.thisdk.tk --ecc --force
acme.sh --renew -d 2048.thisdk.tk --ecc --force
acme.sh --renew -d tomcat.thisdk.tk --ecc --force
```

### Acme.sh 删除证书

```
acme.sh --remove -d docker.thisdk.tk --ecc
acme.sh --remove -d kms.thisdk.tk --ecc
```
