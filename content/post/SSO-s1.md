---
title: "单点登录系列(一)"
date: 2020-03-23T17:17:04+08:00
archives: "2020"
tags: ['php', 'SSO']
author: John SMITH
---

### 单点登录出现的背景
企业内的办公软件繁多，每个软件都有一套自己的用户系统，每个系统都需要注册一个账号，对员工来说需要记录许多账号密码，难以使用，对企业来说需要管理多系统的用户，难以管理。如果有一个系统实现用户的统一管理，登录企业内的所有软件只需要一个账号，对员工和企业都是好事。  
单点登录的出现就解决了这个问题。  
### 单点登录的实现方式
单点登录实现的核心就是多应用共享会话，共享会话的实现方式因协议而异，目前主要有三种协议。
 
1. SAML(Security Assertion Markup Language)  
SAML 是一种基于 XML 结构提供应用间交换用户信息的协议。下面放一张 SAML 单点登录的流程图。
![SAML Web Broswer](https://hurryking.github.io/img/SAML-SSO-Process.gif) 
其中有三个角色， Client(客户端),Identify Provider(认证服务提供者),Service Provider(服务提供者)，SP 提供服务，IP 负责认证
SAML协议 比较复杂，上面的流程图只是 web 浏览器的访问流程，还有 ECP(Enchaned Client Or Proxy) 加强型客户端或代理，IDP(Identity Provider Discovery) 认证服务发现等几种模式。

2. QIDC(OpenID Connect)  
OpenId Connect 是基于 OAuth 2.0 协议的一种简单认证层协议。它允许客户端通过授权服务去校验终端用户的身份，并通过易上手的 REST 风格的接口获取用户的基本信息。

3. LDAP(Lightweight Directory Access Protocol)

### PHP 实现

参考资料  
[0] [SSO](https://auth0.com/docs/sso/current) 
[1] [SAML 官方介绍](http://saml.xml.org/wiki/saml-introduction)
[2] [QIDC 官方介绍](https://openid.net/connect/)
