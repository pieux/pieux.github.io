---
layout: post
title: "Maven Archetype Plugin"
date: 2013-12-08 01:31
comments: true
categories: [Java, Maven]
---


## Struts 2 Maven Archetype

### The Blank Archetype (struts2-archetype-blank)

### The Starter Archetype (struts2-archetype-starter)

Compared to the former, it adds more features like "Spring Integration".

### The Blank Convention Archetype (struts2-archetype-convention)

Features like "Convention-based validation", "GAE aware".

## Creating an Application Using a Maven Archetyp

```
mvn archetype:generate -B \
                       -DgroupId=tutorial \
                       -DartifactId=tutorial \
                       -DarchetypeGroupId=org.apache.struts \
                       -DarchetypeArtifactId=struts2-archetype-blank \
                       -DarchetypeVersion=<version>
```

If the above command will fail because of missing archetypes in central repository, you can try to use staging repository like below

```
mvn archetype:generate -DarchetypeCatalog=http://people.apache.org/builds/struts/<version>/m2-staging-repository/
```
