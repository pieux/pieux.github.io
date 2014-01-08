---
layout: post
title: "Gradle Installation on Mac OS X"
date: 2014-01-06 18:55:29 +0800
comments: true
categories: [Gradle]
---

# Installation

refer [link](http://www.gradle.org/installation)

 看文档发现了一个好玩的东西：

**For Un*x users**

You need a GNU compatible tool to unzip Gradle, if you want the file permissions to be properly set. We mention this as some zip front ends for Mac OS X don't restore the file permissions properly.

 权限要正确被设置，注意下。

**ENVIRONMENT VARIABLES**

add `GRADLE_HOME/bin` to PATH.

**TEST INSTALLATION**

`gradle -v`

# Build Scripts Basics

basic concepts: *projects* and *tasks*.

below is the `build.gradle` of
[JakeWharton/hugo](https://github.com/JakeWharton/hugo). Take is as a
start to learn gradle.

```
allprojects {
  buildscript {
    repositories {
      mavenCentral()
    }
  }

  dependencies {
    repositories {
      mavenCentral()
    }
  }

  group = GROUP
  version = VERSION_NAME

  apply plugin: 'maven'
}

task wrapper(type: Wrapper) {
  gradleVersion = '1.8'
}

task cleanExample(type: Exec) {
  executable = 'gradle'
  workingDir = project.file('hugo-example')
  args = [ 'clean' ]
}

task assembleExample(type: Exec) {
  executable = 'gradle'
  workingDir = project.file('hugo-example')
  args = [ 'assemble' ]
}

task installExample(type: Exec) {
  executable = 'gradle'
  workingDir = project.file('hugo-example')
  args = [ 'installDebug' ]
}
```
