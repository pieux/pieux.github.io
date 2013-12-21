---
layout: post
title: "NOTE: Everything about CAS"
date: 2013-12-08 02:12
comments: true
categories: [CAS, Java]
---

假设 CAS Server 的域名是 cas.server.com

CAS Server 的 webapp 是 spring web flow，登陆地址为：https://cas.server.com/cas/login

登出地址为：https://cas.server.com/cas/logout

语言参数为：locale=zh_CN，locale=en

除了配置 CAS Server 和 Client 外，需要配置环境，包括生成和导出导入证书，Tomcat配置SSL等。

## CAS Server

### 原理（认证流程）

![CAS 基本模式][cas_basic_pattern]

**原理**：按照此图来说

[cas_basic_pattern]:http://i.imgur.com/MDSRTIc.jpg

1. 用户访问CAS Client

2. 其配置的 AuthenticationFilter 会拦截此请求，生成 service 参数，并重定向到 CAS Server 的登陆接口，url为 https://cas.server.com/login?service=redirect_url

3. 用户在 CAS Server 输入 Credentials(一般就是用户名和密码)进行身份验证，成功后，CAS Server 会生成认证 cookie，即TGC。（另：TGC作为 cookie之外，也会换存在服务器本地，我猜测是不是服务器运行实例的内存中。）

4. CAS Server还会根据 service 参数，生成 Ticket，即ST。ST会保存在服务器，也会加在 url 后面，重定向回 client。url为 http://client_server_url:port/app_name?ticket=ST-*-*

5. Client 的 AuthenticationFilter 看到 ticket 参数后，会跳过，交由后面的 TicketValidationFilter 来处理。TicketValidationFilter 会利用 httpclient 工具访问 cas server 的 /serviceValidate 接口，将 ticket, service 传入该接口，验证 ticket 的有效性。

6. 如果返回验证成功，就会把用户信息写入 client 的 session 里。

至此，SSO 会话就建立起来了。

结果就是：

1. 用户在同一浏览器访问同一 client，不会去 CAS Server 认证，因为 AuthenticationFilter 会在 session 读取到用户信息。

2. 用户在同一浏览器访问其他 client，AuthenticationFilter 在 session 里读不到用户信息，会去 cas login 接口认证，但是此时 cas login 接口会读取到存储在cas server域名下的 TGC，所以，CAS Server不会跳到登录页，只会根据 service 参数生成一个 ticket，传给 client。然后由TicketValidationFilter做一次交互验证。

### CAS Server 提供的接口

cas server 一共定义了9个接口。client 会通过 url redirect 和 httpclient 的方式和 server 交互。

接口 | 说明
:-----------: | :-----------
/login        | 认证接口
/logout       | 退出接口，销毁 TGC
/validate     | 验证 ticket 接口，CAS1.0
/serviceValidate | 同上，CAS2.0
/proxy        | 支持代理功能的接口
/proxyValidate | 同上
/CentralAuthenticationService | 用于和远程的 web services 交互
/remoteLogin  | 认证接口（新增）
/directLogin  | 认证接口（新增）

## 认证相关的术语

- **Credentials** 用户提供的凭证，比如 用户名/密码，证书，IP地址，Cookie值等。

- **AuthenticationHandler** 认证Handler，比如：AbstractUsernamePasswordAuthenticationHandler 负责处理 UsernamePasswordCredentials.

- **CredentialsToPrincipalResolvers** 负责由 Credentials 生成 Principal 对象，每种 CredentialsToPrincipalResolvers 只处理 一种Credentials ，比如 UsernamePasswordCredentialsToPrincipalResolver 负责从 UsernamePasswordCredentials 中取出用户名，然后将其赋给生成的 SimplePrincipal 的 ID 属性。

- **AuthenticationMetaDataPopulators** 负责将 Credentials 的一些属性赋值给 Authentication 的 attributes 属性。

- **Authentication**  Authentication是认证管理器的最终处理结果， Authentication 封装了 Principal ，认证时间，及其他一些属性（可能来自 Credentials）。

- **AuthenticationManager** 认证管理器得到 Credentials 对象后，负责调度AuthenticationHandler 去完成认证工作，最后返回的结果是 Authentication 对象。

- **CentralAuthenticationService** CAS 的服务类，对 Web 层提供了一些方法。该类还负责调用 AuthenticationManager 完成认证逻辑。



