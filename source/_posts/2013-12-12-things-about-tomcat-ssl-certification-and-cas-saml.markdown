---
layout: post
title: "Things about Tomcat SSL Certification and CAS(SAML)"
date: 2013-12-12 03:07
comments: true
categories: [CAS, Tomcat, SSL, Java]
---

这里是对Tomcat配置证书（生成/导出/导入）的 spike 总结。也不一定是总结
啦。

会总结哪些方面呢？

1. keytool 相关的命令和作用；
2. Tomcat 的 SSL 配置；
3. 什么是证书？
4. 服务端和客户端都在本地的配置（开发配置）；
5. 服务端部署在其他域名上；
6. etc.

目前想到的几个问题：
- `~/.keystore`，为什么 client 会找这个文件？

- `self-signed certificate`

## CAS Java Client 配置 SAML 1.1

在 client 需要添加这些依赖：

* cas-client-core-3.1.3.jar
* commons-codec-1.4.jar
* commons-logging-1.1.jar
* opensaml-1.1.jar
* xmlsec-1.4.0.jar
* log4j-1.2.15.jar

关于版本，core的话，目前最新的是 3.2。opensaml肯定使用 1.1，因为是
SAML1.1 协议。

其他几个包是什么呢？不需要深入了解，从名称猜测，commons-codec 提供了一
些 encode，decode 方法。xmlsec 应该是安全的XML。

cas-client-core
http://mvnrepository.com/artifact/org.jasig.cas

commons-codec
http://mvnrepository.com/artifact/commons-codec/commons-codec

opensaml
http://mvnrepository.com/artifact/org.opensaml/opensaml/1.1

xmlsec
http://mvnrepository.com/artifact/org.apache.santuario/xmlsec

## Tomcat 配置 SSL

关于 Tomcat 配置 SSL

### 什么是 SSL?

> 主要参考文章 [SSL简介](http://www.sunchis.com/html/java/javaweb/2010/0313/68.html)

SSL(Server Socket Layer)

> SSL（Server Socket Layer）是一种保证网络上的两个节点进行安全通信的协议。IETF（Internet Engineering Task Force）组织对SSL作了标准化，制订了RFC2246规范，并将其称为TLS（Transport Layer Security）。从技术上讲，目前TLS 1.0与TLS 3.0的差别非常微小。

从引用看出一个经常看到的术语：TLS，就是标准化的 SSL 通信协议。换句话说，
TLS 是基于 SSL 开发的。准确地说，TLS 1.0 替代了 SSL 2.0，并且 TLS 1.0
和 SSL 3.0 的差别不大，但不能忽操作。

这方面的参考链接：

1. [SSL versus TLS – What’s the difference?](http://luxsci.com/blog/ssl-versus-tls-whats-the-difference.html)

2. [TLS vs. SSL][tls_vs_ssl]

[tls_vs_ssl]: http://msdn.microsoft.com/en-us/library/windows/desktop/aa380515(v=vs.85).aspx

3. [SSL/TLS 协议详解](http://www.cnblogs.com/adforce/archive/2012/11/27/2790937.html)

SSL和TLS建立在TCP/IP协议的基础上，一些应用层协议，如HTTP和IMAP协议都可
以采用SSL来保证通信的安全。建立在SSL协议上的HTTP被称为HTTPS协议。HTTP
使用的默认端口为80，而HTTPS使用的默认端口为443。

协议层|协议
:---------:|:-------:
应用层|HTTP、IMAP、NNTP、Telnet、FTP等
安全套接字层|SSL、TLS
传输层|TCP
网络层|IP

SSL采用加密技术来实现安全通信，保证通信数据的保密性和完整性，并且保证
通信双方可以验证对方的身份。

### SSL 中的证书

除了对数据通信进行加密外，SSL还采用了身份认证机制，确保通信双方都可以
验证对方的真实身份。

SSL通过安全证书来证明客户或服务器的身份。当客户通过安全的连接和服务器
通信时，服务器会先向客户出示它的安全证书，这个证书声明该服务器是安全的，
而且的确是这个服务器。每一个证书在全球范围内都是唯一的，其他非法服务器
无法假冒原始服务器的身份。可以把安全证书比做电子身份证。

获取安全证书是一件麻烦的事情。一些服务器会向客户出示自己的安全证书，但
另一方面，为了扩大客户群并且便于客户访问，许多服务器不要求客户出示安全
证书。在某些情况下，服务器也会要求客户出示安全证书，以便核实客户的身份，
这主要用于B2B（Business to Business）事务中。

获取安全证书有两种方式，一种方式是从权威机构购买证书，还有一种方式是创建自我签名的证书。

**1 从权威机构获得证书**

安全证书可以有效地保证通信双方身份的可信性。安全证书采用加密技术制作而
成，他人几乎无法伪造。安全证书由国际权威的证书机构（Certificate
Authority，CA）如VeriSign（www.verisign.com）和Thawte（www.thawte.com）
颁发，它们保证了证书的可信性。申请安全证书时，必须支付一定的费用。一个
安全证书只对一个IP地址有效，如果用户的系统环境中有多个IP，那么必须为每
个IP地址购买安全证书。

**2 创建自我签名证书**

在某些场合，通信双方只关心数据在网络上可以安全传输，并不需要对对方进行
身份验证，在这种情况下，可以创建自我签名（self-assign）的证书，比如通
过SUN公司提供的keytool工具就可以创建这样的证书。

即，只需要数据加密传输，而不需要额外的一层身份校验。

## 使用 keytool 工具

这方面的参考文章很多。

对于 .Net，请参考：
[Yale CAS + .net Client 实现 SSO（1）](http://www.cnblogs.com/zhenyulu/archive/2013/01/22/2870838.html)

对于 Java，请参考：
[CAS实现单点登录（SSO）经典完整教程](http://blog.csdn.net/small_love/article/details/6664831)
。

这里唯一需要注意的一点是：生成证书时，要求输入名称和姓氏(First Name
and Last Name)时，需要输入部署 cas server 的计算机名，比如：
pieux-macbook-pro.local 或者 tech-w-xishanshan。在后面的配置 cas
client 的 Authentication Filter 和 Validation Filter 时，配置的服务器
地址应该也是以计算机名开头，不要使用 localhost 或者 ip：

```
<init-param>
    <param-name>casServerLoginUrl</param-name>
    <param-value>https://pieux-macbook-pro.local:8443/cas/login</param-value>
</init-param>
...
<filter-name>CAS Validation Filter</filter-name>
<filter-class>org.jasig.cas.client.validation.Saml11TicketValidationFilter</filter-class>
<init-param>
    <param-name>casServerUrlPrefix</param-name>
    <param-value>https://pieux-macbook-pro.local:8443/cas</param-value>
</init-param>
```

## 自我签名证书在开发中的注意事项

如果 cas server 和 cas client 都部署在相同的机器上（一般是开发过程中），
那如果你按照上面和上面提到的参考文章的步骤，就应该不会有错误了。

但是，有一个大坑。如果 cas server 和 cas client 不是部署在一台机器上，
你很有可能会遇到一个**异常**是：

```
sun.security.validator.ValidatorException: PKIX path building failed:
```

 The problem here is that the CAS client does not trust the
 certificate presented by the CAS server; most often this occurs
 because of using a self-signed certificate on the CAS server.

该问题一般是 cas client 不信任 cas server 展示的证书，而且绝大多数情况
下（按我目前遇到的，就是所有情况下），是 cas server 向 client 展示的是
self-signed certificate。

而且，使用SAML11的时候会报错（难道是SAML11也需要检验证书的真实性？），
使用CAS20就不会报错。因为按理说，SSL可以不需要证书，只利用加密通信的特
性。这块持续挖掘下。

那怎么解决这个问题呢？很简单，让 client 信任 server 的证书就行了。把
server 端生成的证书拷贝到 client 端部署的机器上，然后用 keytool 工具导
入到 client 机器 JDK 的 cacerts 里。

## SSL握手通信过程

> 主要参考文章 [SSL握手通信过程](http://www.sunchis.com/html/java/javaweb/2010/0313/68.html)

暂时先不了解。

## 什么是 `.keystore` 文件？

