---
layout: post
title: "Deploy war to Tomcat"
date: 2013-12-05 02:28
comments: true
categories: [Java]
---

非常简单，`mvn package` 生成的 war 包，直接扔进 Tomcat 安装目录下的 webApps 目录。然后重启下 Tomcat。

不需要任何额外的配置，默认是 localhost:8080/war_name/
