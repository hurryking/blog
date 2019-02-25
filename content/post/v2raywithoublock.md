---
title: "如何科学上网"
date: 2019-02-23T17:41:55+08:00
archives: "2019"
tags: []
author: John SMITH
---

## 墙的工作方式
 [维基百科简介](https://zh.wikipedia.org/wiki/%E9%98%B2%E7%81%AB%E9%95%BF%E5%9F%8E) 等你翻过去就知道了 ^_^ 
## 如何科学上网
需要准备的东西

1. 购买vps(建议买国外的，国内受监管) 如果有就跳过此步

    推荐[购买地址](https://m.do.co/c/e9484b4d33df)
    
    
    服务端要求的要求操作系统为**Ubuntu 16+ / Debian 8+ / CentOS 7+**，所以vps选操作系统时注意下不要选错了
2. 购买域名(建议买国外的，国内需要很长的备案时间) 

    推荐[godaddy](https://sg.godaddy.com/zh/)
    
    购买完成后记得配置好DNS，把域名指向刚才购买的域名的外网ip，在这里假设购买的域名为 example.com  (**大家记得修改成自己购买的域名不要直接复制**)
    
    [DNS相关知识](https://blog.csdn.net/huwei2003/article/details/54580614) 希望能帮到你

3. paypal账号(为了结账 [注册地址](https://www.paypal.com))

    如果有迷惑的这个是官方的[帮助链接](https://www.paypal.com/c2/smarthelp/article/how-do-i-add-a-credit-or-debit-card-to-my-paypal-account-faq826) 希望能帮到你
    
4. 一定的时间和耐心
   
    顺利的话大约一个小时

vps购买好以后, 需要安装的软件分服务端和客户端

### 安装服务端
我们先来安装服务端软件
服务端需要安装的软件

1. nginx(用来转发流量，起混淆作用)

2. v2ray(充当代理角色)

下面来给出一副图说明下基本工作原理
![](https://user-gold-cdn.xitu.io/2019/2/24/1691c1693dfee5ae?w=966&h=600&f=png&s=88229)
​    **以下所有操作建议用root执行**

#### 安装Nginx并配置给站点ssl证书
  我们先来安装nginx
  nginx 安装脚本 [在这里](http://mirrors.linuxeye.com/oneinstack-full.tar.gz) 如果不想看教程直接复制下面这条命令在家目录下执行

```
wget -c http://mirrors.linuxeye.com/oneinstack-full.tar.gz && tar xzf oneinstack-full.tar.gz && ./oneinstack/install.sh --nginx_option 1 --ssh_port 22
```

  nginx 安装完成后 接下来我们来为购买的域名生成虚拟主机配置文件
```
./oneinstack/vhost.sh
```

选择如下图
![](https://user-gold-cdn.xitu.io/2019/2/24/1691dd483cdb3e0a?w=572&h=328&f=png&s=21041)
​    配置完成后我们需要改下生成的nginx配置

```
vi /usr/local/nginx/conf/vhost/example.com.conf
```

​    修改以配置，大约在23行下添加如下配置

![](https://user-gold-cdn.xitu.io/2019/2/24/1691dde2e3caae54?w=598&h=287&f=png&s=14849)

```
location /ray {
      proxy_redirect off;
      proxy_pass http://127.0.0.1:10000;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
      proxy_set_header Host $http_host;
}
```
​    然后重新加载下nginx的配置

```
systemctl reload nginx
```

nginx安装完成后，我们开始安装v2ray

#### 安装v2ray    
v2ray一键安装教程 [在这里](https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC) 感谢大佬 [233boy](https://233blog.com) 为萌新提供便利
​    
&nbsp;&nbsp;&nbsp;&nbsp;协议我们先随便选下(后面我们需要手工改下配置)

&nbsp;&nbsp;&nbsp;&nbsp;端口我们选 **10000**

&nbsp;&nbsp;&nbsp;&nbsp;不配置shadowsock

v2ray安装完成后，我们修改下v2ray的配置

```
vi /etc/v2ray/config.json
```

改为如下配置

    {
      "inbound": {
        "port": 10000,
        "listen":"127.0.0.1",
        "protocol": "vmess",
        "settings": {
          "clients": [
            {
              "id": "改为自己的UUID, 不知道的进命令行使用v2ray info查看用户ID的值",
              "alterId": 64
            }
          ]
        },
        "streamSettings": {
          "network": "ws",
          "wsSettings": {
          "path": "/ray"
          }
        }
      },
      "outbound": {
        "protocol": "freedom",
        "settings": {}
      },
      "outboundDetour": [
        {
          "protocol": "blackhole",
          "settings": {},
          "tag": "blocked"
        }
      ],
      "routing": {
        "strategy": "rules",
        "settings": {
          "rules": [
            {
              "type": "field",
              "ip": [
                "0.0.0.0/8",
                "10.0.0.0/8",
                "100.64.0.0/10",
                "127.0.0.0/8",
                "169.254.0.0/16",
                "172.16.0.0/12",
                "192.0.0.0/24",
                "192.0.2.0/24",
                "192.168.0.0/16",
                "198.18.0.0/15",
                "198.51.100.0/24",
                "203.0.113.0/24",
                "::1/128",
                "fc00::/7",
                "fe80::/10"
              ],
              "outboundTag": "blocked"
            }
          ]
        }
      }
    }

然后重启v2ray

```
v2ray restart
```

至此服务端算是安装配置完成

### 安装客户端
接下来我们开始安装配置客户端

Windows [下载地址](https://github.com/2dust/v2rayN)
Mac [下载地址](https://github.com/yanue/V2rayU)
IOS 去应用商店搜 Kitsunebi 或 i2Ray
Android [apk包下载地址](https://github.com/2dust/v2rayNG/releases/download/0.6.14/app-universal-release.apk)

贴下Windows客户端配置

    {
      "inbound": {
        "port": 1080,
        "listen": "127.0.0.1",
        "protocol": "socks",
        "settings": {
          "auth": "noauth",
          "udp": false
        }
      },
      "outbound": {
        "protocol": "vmess",
        "settings": {
          "vnext": [
            {
              "address": "example.com",
              "port": 443,
              "users": [
                {
                  "id": "和服务端的UUID填成一样的",
                  "alterId": 64
                }
              ]
            }
          ]
        },
        "streamSettings": {
          "network": "ws",
          "security": "tls",
          "tlsSettings": {
            "serverName": "example.com"
          },
          "wsSettings": {
            "path": "/ray"
          }
        }
      },
      "outboundDetour": [
        {
          "protocol": "freedom",
          "settings": {},
          "tag": "direct"
        }
      ],
      "routing": {
        "strategy": "rules",
        "settings": {
          "domainStrategy": "IPIfNonMatch",
          "rules": [
            {
              "type": "field",
              "ip": [
                "0.0.0.0/8",
                "10.0.0.0/8",
                "100.64.0.0/10",
                "127.0.0.0/8",
                "169.254.0.0/16",
                "172.16.0.0/12",
                "192.0.0.0/24",
                "192.0.2.0/24",
                "192.168.0.0/16",
                "198.18.0.0/15",
                "198.51.100.0/24",
                "203.0.113.0/24",
                "::1/128",
                "fc00::/7",
                "fe80::/10"
              ],
              "outboundTag": "direct"
            },
            {
              "type": "chinasites",
              "outboundTag": "direct"
            },
            {
              "type": "chinaip",
              "outboundTag": "direct"
            }
          ]
        }
      }
    }

配置的大概意思是把请求发给example.com的443端口，传输协议使用ws(websocket)，使用TLS加密，websocket的域名为 example.com，websocket的路径为/ray
其他客户端的配置类似就不一一写出来了，主要原因还是没有条件 ^_^

配置完成后打开 chrome 浏览器，安装 Proxy SwitchyOmega 扩展，配置代理协议为sock5 地址 127.0.0.1 端口为 1080 切换至 **proxy** 模式

## 结束语
然后打开文首的链接就可以看到墙是怎么工作的了 [维基百科简介](https://zh.wikipedia.org/wiki/%E9%98%B2%E7%81%AB%E9%95%BF%E5%9F%8E) 希望大家遵纪守法不要用来做不利于国家团结的事 ^_^


20190206更新

执行下来有什么问题,可以在我的[掘金](https://juejin.im/user/5729b8681ea4930064b00d6f)帐号下留言
