---
layout: post
title: "NOTE: Special Things about CAS"
date: 2013-12-05 02:49
comments: true
categories: [Java, CAS]
---

# 1
**Create the Certificate**

打开终端，进到 home 目录，运行命令：

```
keytool -genkey -alias tomcat -keyalg RSA -validity 365
```

需要输入：

```
Enter keystore password:
Re-enter new password:
What is your first and last name?
  [Unknown]:  $REPLACE_WITH_FULL_MACHINE_NAME
What is the name of your organizational unit?
  [Unknown]:  Test
What is the name of your organization?
  [Unknown]:  Test
What is the name of your City or Locality?
  [Unknown]:  Test
What is the name of your State or Province?
  [Unknown]:  Test
What is the two-letter country code for this unit?
  [Unknown]:  US
Is CN=$FULL_MACHINE_NAME, OU=Test, O=Test, L=Test, ST=Test, C=US correct?
  [no]:  yes
```

如何给这些值？

For the keystore password you should enter "changeit" without the quotation marks. When prompted for the first and last name, you should enter your machine name during development. The rest of the data does not matter. Then obviously answer "yes" to the question of whether it's correct.

> 注意： CAS 协议需要走 HTTPS，为了保证能够工作， "first and last name" 为 $FULL_MACHINE_NAME。Mac 上获取计算机全名的方法是：`scutil --get ComputerName` 或者 `scutil --get LocalHostName`。

下一步，打开 `$TOMCAT_HOME/conf/server.xml`，找到这一块，去掉注释即可：

```
<!--
<Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
    maxThreads="150" scheme="https" secure="true"
    clientAuth="false" sslProtocol="TLS" />
-->
```

重启 Tomcat，访问

```
https://$FULL_MACHINE_NAME:8443/
```

Any application that wishes to securely connect to this Tomcat instance would need to import the certificate. You can export the certificate that's compatible with other JVM keystores by executing the following command:

所有想访问该 Tomcat 实例的应用都需要导入证书。首先导出证书，需要输入 keystore 的密码，这里就是 changeit：

```
keytool -export -alias tomcat -file server.crt
```

输出是：

```
Enter keystore password:
Certificate stored in file <server.crt>
```

You can then import the server.crt into other JVM keystore's by executing a command similar to this:

```
keytool -import -file server.crt -keystore $JAVA_HOME/jre/lib/security/cacerts -alias tomcat
```

It's recommended that you add it to the JVM keystore of your local development machine to facilitate testing.

# 2
**Maven War Overlay**[^1]

[^1]: [Best Practice - Setting Up CAS Locally using the Maven WAR Overlay Method](https://wiki.jasig.org/display/CASUM/Best+Practice+-+Setting+Up+CAS+Locally+using+the+Maven+WAR+Overlay+Method)
