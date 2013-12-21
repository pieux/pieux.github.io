---
layout: post
title: "First Touch of Travis CI"
date: 2013-12-11 02:37
comments: true
categories: [Tools]
---

目前 Travis CI 给我的感觉和 Jekins 类似，每次 push 到 github 上，
Travis 会自动跑一遍，比如按官方文档所说，对于 Java 项目，如果发现
pom.xml 文件，会使用 Maven 3 的一些 lifecycle。

对于一个 Java 项目，需要在 .git/ 目录下，新建文件 `.travis.yml`，最简
单的内容是：

```
language: java
```
