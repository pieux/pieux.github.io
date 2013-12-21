---
layout: post
title: "A example of writing Exception in java"
date: 2013-11-23 07:49
comments: true
categories: [Java]
---

记录在阅读 Jasig Cas 的源码学习到的 Java 知识。


```
public abstract class AuthenticationException extends Exception {

```
首先，这是一个 abstract class.

```
/** The code to return for resolving to a message description. */
private String code;

/**
 * Method to return the unique identifier for this error type.
 *
 * @return the String identifier for this error type.
 */
public final String getCode() {
    return this.code;
}

```
使用 code，来解析异常消息的描述。
并且，code 有一个 get 方法，final 不可继承。

```
/** The error type that provides additional info about the nature of the exception cause **/
private String type = "error";

/**
 * Method to return the error type of this exception
 *
 * @return the String identifier for the cause of this error.
 */
public final String getType() {
    return this.type;
}
```
额外的一个 field: type，维护导致异常的本质信息。

下面是 `AuthenticationException` 的 constructors。

```
public AuthenticationException(final String code) {
    this.code = code;
}
```
这是第1个 constructor。

```
public AuthenticationException(final String code, final String msg) {
    super(msg);
    this.code = code;
}

public AuthenticationException(final String code, final String msg, final String type) {
    super(msg);
    this.code = code;
    this.type = type;
}
```
第2个和第3个 constructor，需要学习的是 `super(msg)`。

```
/**
 * Constructor that takes a code description of the error and the chained
 * exception. These codes normally have a corresponding entries in the
 * messages file for the internationalization of error messages.
 *
 * @param code The short unique identifier for this error.
 * @param throwable The chained exception for this AuthenticationException
 */
public AuthenticationException(final String code, final Throwable throwable) {
    super(throwable);
    this.code = code;
}
```
最后一个 constructor，需要学习的是 `final Throwable throwable`，这块不是很懂。按描述是能链式异常。
查看了 [Java Exception 文档][1]，它有一个 constructor：

[1]:http://docs.oracle.com/javase/6/docs/api/java/lang/Exception.html#Exception(java.lang.Throwable)

```
public Exception(Throwable cause)
```
它的作用是：用指定的 cause 和 详细的 message(`cause==null ? null : cause.toString()`)构造新的异常。
它的作用就是一个 "wrapper"。


完整源码见：

```
/*
 * Licensed to Jasig under one or more contributor license
 * agreements. See the NOTICE file distributed with this work
 * for additional information regarding copyright ownership.
 * Jasig licenses this file to you under the Apache License,
 * Version 2.0 (the "License"); you may not use this file
 * except in compliance with the License.  You may obtain a
 * copy of the License at the following location:
 *
 *   http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing,
 * software distributed under the License is distributed on an
 * "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
 * KIND, either express or implied.  See the License for the
 * specific language governing permissions and limitations
 * under the License.
 */
package org.jasig.cas.authentication.handler;

/**
 * The most generic type of authentication exception that one can catch if not
 * sure what specific implementation will be thrown. Top of the tree of all
 * other AuthenticationExceptions.
 *
 * @author Scott Battaglia
 * @version $Revision$ $Date$
 * @since 3.0
 */
public abstract class AuthenticationException extends Exception {

    /** Serializable ID. */
    private static final long serialVersionUID = 3906648604830611762L;

    /** The code to return for resolving to a message description. */
    private String code;

    /** The error type that provides additional info about the nature of the exception cause **/
    private String type = "error";

    /**
     * Constructor that takes a code description of the error. These codes
     * normally have a corresponding entries in the messages file for the
     * internationalization of error messages.
     *
     * @param code The short unique identifier for this error.
     */
    public AuthenticationException(final String code) {
        this.code = code;
    }

    /**
     * Constructor that takes a <code>code</code> description of the error along with the exception
     * <code>msg</code> generally for logging purposes. These codes normally have a corresponding
     * entries in the messages file for the internationalization of error messages.
     *
     * @param code The short unique identifier for this error.
     * @param msg The error message associated with this exception for additional logging purposes.
     */
    public AuthenticationException(final String code, final String msg) {
        super(msg);
        this.code = code;
    }

    /**
     * Constructor that takes a <code>code</code> description of the error along with the exception
     * <code>msg</code> generally for logging purposes and the <code>type</code> of the error that originally caused the exception.
     * These codes normally have a corresponding entries in the messages file for the internationalization of error messages.
     *
     * @param code The short unique identifier for this error.
     * @param msg The error message associated with this exception for additional logging purposes.
     * @param type The type of the error message that caused the exception to be thrown. By default,
     * all errors are considered of <code>error</code>.
     */
    public AuthenticationException(final String code, final String msg, final String type) {
        super(msg);
        this.code = code;
        this.type = type;
    }

    /**
     * Constructor that takes a code description of the error and the chained
     * exception. These codes normally have a corresponding entries in the
     * messages file for the internationalization of error messages.
     *
     * @param code The short unique identifier for this error.
     * @param throwable The chained exception for this AuthenticationException
     */
    public AuthenticationException(final String code, final Throwable throwable) {
        super(throwable);
        this.code = code;
    }

    /**
     * Method to return the error type of this exception
     *
     * @return the String identifier for the cause of this error.
     */
    public final String getType() {
        return this.type;
    }

    /**
     * Method to return the unique identifier for this error type.
     *
     * @return the String identifier for this error type.
     */
    public final String getCode() {
        return this.code;
    }

    @Override
    public final String toString() {
        String msg = getCode();
        if (getMessage() != null && getMessage().trim().length() > 0)
            msg = ":" + getMessage();
        return msg;
    }

}

```



