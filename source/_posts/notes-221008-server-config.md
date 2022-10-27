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

### Xray Server自用配置文件
``` json
{
  "log": {
    "loglevel": "wraning"
  },
  "inbounds": [
    {
      "port": 443,
      "protocol": "vless",
      "settings": {
        "clients": [
          {
            "id": "",
            "flow": "xtls-rprx-direct",
            "level": 0,
            "email": "thisdk@gmail.com"
          }
        ],
        "decryption": "none",
        "fallbacks": [
          {
            "name": "www.thisdk.tk",
            "path": "/wechat/honor_of_kings.ws",
            "dest": 1081,
            "xver": 1
          },
          {
            "name": "www.thisdk.tk",
            "dest": 1082,
            "xver": 1
          },
          {
            "name": "tomcat.thisdk.tk",
            "dest": "tomcat:8080",
            "xver": 0
          },
          {
            "name": "frp.thisdk.tk",
            "dest": "frps:7500",
            "xver": 0
          },
          {
            "name": "2048.thisdk.tk",
            "dest": "docker.2048:80",
            "xver": 0
          }
        ]
      },
      "streamSettings": {
        "network": "tcp",
        "security": "xtls",
        "xtlsSettings": {
          "alpn": [
            "http/1.1"
          ],
          "certificates": [
            {
              "certificateFile": "/etc/xray/tls/www.thisdk.tk.cer",
              "keyFile": "/etc/xray/tls/www.thisdk.tk.key"
            },
            {
              "certificateFile": "/etc/xray/tls/frp.thisdk.tk.cer",
              "keyFile": "/etc/xray/tls/frp.thisdk.tk.key"
            },
            {
              "certificateFile": "/etc/xray/tls/2048.thisdk.tk.cer",
              "keyFile": "/etc/xray/tls/2048.thisdk.tk.key"
            },
            {
              "certificateFile": "/etc/xray/tls/tomcat.thisdk.tk.cer",
              "keyFile": "/etc/xray/tls/tomcat.thisdk.tk.key"
            }
          ],
          "rejectUnknownSni": true
        }
      }
    },
    {
      "port": 1081,
      "listen": "127.0.0.1",
      "protocol": "vless",
      "settings": {
        "clients": [
          {
            "id": "",
            "level": 0,
            "email": "thisdk@gmail.com"
          }
        ],
        "decryption": "none"
      },
      "streamSettings": {
        "network": "ws",
        "security": "none",
        "wsSettings": {
          "acceptProxyProtocol": true,
          "path": "/wechat/honor_of_kings.ws"
        }
      }
    },
    {
      "port": 1082,
      "listen": "127.0.0.1",
      "protocol": "trojan",
      "settings": {
        "clients": [
          {
            "password": "",
            "level": 0,
            "email": "thisdk@gmail.com"
          }
        ],
        "fallbacks": [
          {
            "dest": "portainer:9000"
          }
        ]
      },
      "streamSettings": {
        "network": "tcp",
        "security": "none",
        "tcpSettings": {
          "acceptProxyProtocol": true
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom"
    }
  ]
}
```

### Xray Client自用配置文件

``` json
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 7890,
            "listen": "127.0.0.1",
            "protocol": "http",
            "settings": {
                "udp": true
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "vless",
            "settings": {
                "vnext": [
                    {
                        "address": "",
                        "port": 443,
                        "users": [
                            {
                                "id": "",
                                "flow": "xtls-rprx-direct",
                                "encryption": "none",
                                "level": 0,
                                "email": "thisdk@gmail.com"
                            }
                        ]
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp",
                "security": "xtls",
                "xtlsSettings": {
                    "serverName": "www.thisdk.tk",
                    "rejectUnknownSni": true,
                    "fingerprint": "chrome"
                }
            }
        }
    ]
}
```

``` json
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 7890,
            "listen": "127.0.0.1",
            "protocol": "http",
            "settings": {
                "udp": true
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "vless",
            "settings": {
                "vnext": [
                    {
                        "address": "",
                        "port": 443,
                        "users": [
                            {
                                "id": "",
                                "encryption": "none",
                                "level": 0
                            }
                        ]
                    }
                ]
            },
            "streamSettings": {
                "network": "ws",
                "security": "tls",
                "tlsSettings": {
                    "serverName": "www.thisdk.tk",
                    "fingerprint": "chrome"
                },
                "wsSettings": {
                    "path": "/wechat/honor_of_kings.ws"
                }
            }
        }
    ]
}
```