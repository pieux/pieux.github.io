---
layout: post
title: "jasig cas server cacerts"
date: 2014-01-08 10:49:34 +0800
comments: true
categories: [CAS, SSL]
---

由于 SSO 域名切换成好记的域名，需要重新生成证书，并导入：

#  删除 Client 中曾导入的旧的证书

先找到 CAS Client 部署服务器的
存储 所有信任证书（cacerts） 的位置：`/usr/local/jdk/jre/lib/security/cacerts`

 从中删除原来导入的 cacert。 注： 之前 这里取的别名是 smalllove，这次
 重新取个好记的名字 ssoalpha

```
[dianping@host_214-74 security]$ keytool -delete -trustcacerts -alias smalllove -keystore /usr/local/jdk/jre/lib/security/cacerts -storepass changeit
```

# Server 重新生成证书

```
keytool -genkey -alias ssoalpha -keyalg RSA -storepass changeit  -keystore ~/keys/.keystore -validity 3600
```

```
[dianping@host_214-124 ~]$ keytool -genkey -alias ssoalpha -keyalg RSA -storepass changeit  -keystore ~/keys/.keystore -validity 3600
您的名字与姓氏是什么？
  [Unknown]：  sso.a.alpha.dp
您的组织单位名称是什么？
  [Unknown]：  ssoalpha
您的组织名称是什么？
  [Unknown]：  ssoalpha
您所在的城市或区域名称是什么？
  [Unknown]：  ssoalpha
您所在的州或省份名称是什么？
  [Unknown]：  ssoalpha
该单位的两字母国家代码是什么
  [Unknown]：  CN
CN=sso.a.alpha.dp, OU=ssoalpha, O=ssoalpha, L=ssoalpha, ST=ssoalpha, C=CN 正确吗？
  [否]：  y

输入<ssoalpha>的主密码
    （如果和 keystore 密码相同，按回车）：
[dianping@host_214-124 ~]$
```

# JBOSS 配置 SSL

因为重新生成了 keystore，而 JBoss 的 SSL 配置有一部分需要用到
`keystore` 文件。

JBoss 配置 SSL 需要动到的文件：
`$JBOSS_HOME/server/default/deploy/jboss-web.deployer/server.xml`

 其中的 keystoreFile 的位置是：`$JBOSS_HOME/server/default/conf/server.keystore`。

 这个 keystore 就是生成证书时生成的。

```
    <!-- Define a SSL HTTP/1.1 Connector on port 8443
         This connector uses the JSSE configuration, when using APR, the
         connector should be using the OpenSSL style configuration
         described in the APR documentation -->

    <Connector port="8443" address="${jboss.bind.address}"
                protocol="HTTP/1.1" SSLEnabled="true"
               maxThreads="150" scheme="https" secure="true"
               clientAuth="false" sslProtocol="TLS"
               keystoreFile="${jboss.server.home.dir}/conf/server.keystore"
               keystorePass="changeit"
               />
```

 特别注意下：

```
同样的war在tomcat下运行是没有问题的，但在jboss下却包上面的异常。这说明正常情况下HibernatePersistence 应该能转换成PersistenceProvider，不能转换的原因就是这两个类是由不同的加载器加载的。搜索jboss classloader查找解决方案，设置server\default\deploy\JBoss-web.deploy\META-INF\JBoss-service.xml 文件中的<attribute name="UseJBossWebLoader">true</attribute>.
```


# Server 导出证书

```
[dianping@host_214-124 keys]$ keytool -export -file ~/keys/ssoalpha.crt -alias ssoalpha -keystore ~/keys/.keystore -storepass changeit
保存在文件中的认证 </home/dianping/keys/ssoalpha.crt>
```

# 使用 SCP 工具从 server 下载证书

注意写成完整路径：

```
scp -P 58422 dianping@192.168.214.124:$1 $2
copy-from-alpha-cas-server /home/dianping/keys/ssoalpha.crt .
```

# 使用 SCP 工具把证书上传到 client 部署机器

```
scp -P 58422 $1 dianping@192.168.214.74:$2
copy-to-alpha-employee-port ./ssoalpha.crt /home/dianping/keys/.
```
# client 导入证书

```
[dianping@host_214-74 keys]$ keytool -import -keystore /usr/local/jdk/jre/lib/security/cacerts -file ./ssoalpha.crt -alias ssoalpha
输入keystore密码：
所有者:CN=sso.a.alpha.dp, OU=ssoalpha, O=ssoalpha, L=ssoalpha, ST=ssoalpha, C=CN
签发人:CN=sso.a.alpha.dp, OU=ssoalpha, O=ssoalpha, L=ssoalpha, ST=ssoalpha, C=CN
序列号:52ccbfcc
有效期: XXX
证书指纹:
     XXX
     签名算法名称:SHA1withRSA
     版本: X
信任这个认证？ [否]：  y
认证已添加至keystore中
```
