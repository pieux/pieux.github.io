---
layout: post
title: "NOTE: CAS Java Client"
date: 2013-12-05 08:43
comments: true
categories: [CAS, Java]
---

# CAS Client for Java 3.1

## 通过 web.xml 配置 Jasig CAS Client for Java

CAS Client for Java 3.1/3.2 可以通过配置 web.xml 里的 context-params 和 filter init-params。每个 filter 都需要配置一系列的属性（properties）。filters 会查找这些属性：

1. 首先检查 filter 的本地 init-params（local init-params），看是否有相符合的属性名；
2. 其次检查 context 的参数 ，看是否有相符合的属性名；

如果在 filter 的 init-params 和 context 的参数中找到相同的值，则选用 init-params。

同样，filter 的顺序为：

1. SingleLogOutFilter (if you're using it)
- AuthenticationFilter
- TicketValidationFilter (whichever one is chosen)
- HttpServletRequestWrapperFilter
- AssertionThreadLocalFilter

> ! 如果使用 serverName 属性，请注意 fragment-URL(#后的 URL)不会发送给服务器。

一一介绍可用的 filters:

### org.jasig.cas.client.authentication.AuthenticationFilter

AuthenticationFilter 判断一个用户是否需要验证。如果需要，则重定向到 CAS server。

```
<filter>
  <filter-name>CAS Authentication Filter</filter-name>
  <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>
  <init-param>
    <!--cas server login url-->
    <param-name>casServerLoginUrl</param-name>
    <param-value>https://battags.ad.ess.rutgers.edu:8443/cas/login</param-value>
  </init-param>
  <init-param>
    <!--cas server name-->
    <param-name>serverName</param-name>
    <param-value>http://www.acme-client.com</param-value>
  </init-param>
</filter>
```

**Required Properties**

- casServerLoginUrl: CAS server 的登录页地址，i.e. https://localhost:8443/cas/login
- service or serverName
  - service: 发送给 CAS server 的服务的 URL，e.g. https://localhost:8443/yourwebapp/index.html
  - serverName: 应用部署的服务器名。service URL 会据此动态生成。i.e. https://localhost:8443 (需要指定协议，如果是标准端口，则端口可隐式)。

**Optional Properties**: 

有 *renew*, *gateway*, *artifactParameterName*, *serviceParameterName*。

### org.jasig.cas.client.authentication.Saml11AuthenticationFilter

猜测是支持 SAML 1.1 的 authentication filter。

```
<filter>
  <filter-name>CAS Authentication Filter</filter-name>
  <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>
  <init-param>
    <param-name>casServerLoginUrl</param-name>
    <param-value>https://battags.ad.ess.rutgers.edu:8443/cas/login</param-value>
  </init-param>
  <init-param>
    <param-name>serverName</param-name>
    <param-value>http://www.acme-client.com</param-value>
  </init-param>
</filter>
```

### org.jasig.cas.client.validation.Saml11TicketValidationFilter

使用 SAML 1.1 协议验证 tickets。

```
<filter>
  <filter-name>CAS Validation Filter</filter-name>
  <filter-class>org.jasig.cas.client.validation.Saml11TicketValidationFilter</filter-class>
  <init-param>
    <param-name>casServerUrlPrefix</param-name>
    <param-value>https://battags.ad.ess.rutgers.edu:8443/cas</param-value>
  </init-param>
  <init-param>
    <param-name>serverName</param-name>
    <param-value>http://www.acme-client.com</param-value>
  </init-param>
 </filter>
```

**Required Properties**

- casServerUrlPrefix: CAS 服务器的URL起始页，i.e. https://localhost:8443/cas
- serverName or service: 同上

**Optional Properties**

- redirectAfterValidation (default: true)
- useSession (default: true)
- exceptionOnValidationFailure (default: true)
- tolerance (default: 1000 mSec)
- renew (default: false)

### org.jasig.cas.client.util.HttpServletRequestWrapperFilter

Wraps an HttpServletRequest so that the getRemoteUser and getPrincipal return the CAS related entries.

包裹 HttpServletRequest，使 getRemoteUser 和 getPrincipal 返回 CAS 相关的入口。

```
<filter>
  <filter-name>CAS HttpServletRequest Wrapper Filter</filter-name>
  <filter-class>org.jasig.cas.client.util.HttpServletRequestWrapperFilter</filter-class>
</filter>
```

**Required Properties**

None

**Optional Properties**

None

### org.jasig.cas.client.util.AssertionThreadLocalFilter

Places the Assertion in a ThreadLocal for portions of the application that need access to it. This is useful when the Web application that this filter "fronts" needs to get the Principal name, but it has no access to the HttpServletRequest, hence making getRemoteUser() call impossible.

把这个 Assertion 放进 ThreadLocal 中，因为有些应用可能需要。比如：当 Web 应用需要拿到 Principal 的名字，但是它无法访问 HttpServletRequest，因此 getRemoteUser() 没有用。

> ! 没搞懂什么意思，猜测是 CAS Client 应用想要拿到登陆的用户名，但是用户名存储在 CAS Server 上。这个 filter 的作用就是把 Principal 放到 ThreadLocal 变量中。

```
<filter>
  <filter-name>CAS Assertion Thread Local Filter</filter-name>
  <filter-class>org.jasig.cas.client.util.AssertionThreadLocalFilter</filter-class>
</filter>
```

e.g. 一份稍完整的 CAS Client for Java 的 web.xml（未使用 SAML 1.1，为 CAS 协议）

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
    version="2.4">

    <display-name>CAS client demo : application</display-name>

    <filter>
        <!--配置 AuthenticationFilter-->
        <filter-name>CAS Authentication Filter</filter-name>
        <filter-class>org.jasig.cas.client.authentication.AuthenticationFilter</filter-class>
        <init-param>
            <!--property: 登陆页配置-->
            <param-name>casServerLoginUrl</param-name>
            <param-value>http://localhost:8080/cas/login</param-value>
        </init-param>
        <init-param>
            <!--property: 部署服务器地址-->
            <param-name>serverName</param-name>
            <param-value>http://localhost:8080</param-value>
        </init-param>
        <!-- init-param>
            <param-name>service</param-name>
            <param-value>http://localhost:8080/default.jsp</param-value>
        </init-param-->
    </filter>

    <filter>
        <filter-name>CAS Validation Filter</filter-name>
        <filter-class>org.jasig.cas.client.validation.Cas10TicketValidationFilter</filter-class>
        <init-param>
            <param-name>casServerUrlPrefix</param-name>
            <param-value>http://localhost:8080/cas</param-value>
        </init-param>
        <init-param>
            <param-name>serverName</param-name>
            <param-value>http://localhost:8080</param-value>
        </init-param>
    </filter>

    <filter-mapping>
        <filter-name>CAS Authentication Filter</filter-name>
        <url-pattern>/protected/*</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>CAS Validation Filter</filter-name>
        <url-pattern>*</url-pattern>
    </filter-mapping>

</web-app>
```

e.g. 使用 SAML 1.1

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
	version="2.4">

	<display-name>CAS client demo : application</display-name>

	<filter>
		<filter-name>CAS Authentication Filter</filter-name>
		<filter-class>org.jasig.cas.client.authentication.Saml11AuthenticationFilter</filter-class>
		<init-param>
			<param-name>casServerLoginUrl</param-name>
			<param-value>http://localhost:8080/cas/login</param-value>
		</init-param>
		<init-param>
			<param-name>serverName</param-name>
			<param-value>http://localhost:8080</param-value>
		</init-param>
		<init-param>
			<param-name>onlyFullyAuthenticated</param-name>
            <param-value>true</param-value>
        </init-param>
	</filter>

	<filter>
		<filter-name>CAS Validation Filter</filter-name>
		<filter-class>org.jasig.cas.client.validation.Saml11TicketValidationFilter</filter-class>
		<init-param>
			<param-name>casServerUrlPrefix</param-name>
			<param-value>http://localhost:8080/cas</param-value>
		</init-param>
		<init-param>
			<param-name>serverName</param-name>
			<param-value>http://localhost:8080</param-value>
		</init-param>
	</filter>

	<filter-mapping>
		<filter-name>CAS Authentication Filter</filter-name>
		<url-pattern>/protected/*</url-pattern>
	</filter-mapping>

	<filter-mapping>
		<filter-name>CAS Validation Filter</filter-name>
		<url-pattern>*</url-pattern>
	</filter-mapping>

</web-app>
```


## 配置单点登出 Single Sign Out

> ! *SingleSignOutFilter* 会影响到 character encoding。建议显式地配置下 [VT Character Encoding Filter][1] 或 [Spring Character Encoding Filter][2]。

[1]:http://code.google.com/p/vt-middleware/wiki/vtservletfilters#CharacterEncodingFilter
[2]:http://static.springsource.org/spring/docs/2.0.x/api/org/springframework/web/filter/CharacterEncodingFilter.html

CAS 对 Single Sign Out support 的支持，涉及到对一个 filter 和一个 ContextListener 的配置。需要注意的一点是，如果以 Web filters 的形式为 Java 配置 CAS Client，登出的 filter 需要在其他 filters 前面。

**PS**: Order of Required Filters [全文链接](https://wiki.jasig.org/display/CASC/CAS+Client+for+Java+3.1)
Order of Required Filters

How to configure the filters is described on the pages above. This section details the order in which the filters should appear:

1. SingleLogOutFilter (if you're using it)
- AuthenticationFilter
- TicketValidationFilter (whichever one is chosen)
- HttpServletRequestWrapperFilter
- AssertionThreadLocalFilter

```
<listenerclass>
	org.jasig.cas.client.session.SingleSignOutHttpSessionListener</listener-class>
</listener>
```
