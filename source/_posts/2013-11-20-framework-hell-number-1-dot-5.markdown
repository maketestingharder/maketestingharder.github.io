---
layout: post
title: "framework hell #1.5"
date: 2013-11-20 20:56:55 +0200
comments: true
categories:
  - cucumber
  - groovy
  - java
  - jbehave
  - junit
  - maven
  - pom
  - spock
  - surefire
  - testng
  - tutorial
---
Next post was supposed to be about pom.xml details... but it won't.
Since I used Spock for Groovy tests why should't I also include Cucumber and JBehave

So today I'm going to fix this omission.

<!-- more -->

First let's add dependencies to pom file.

``` xml
<dependency>
    <groupId>org.jbehave</groupId>
    <artifactId>jbehave-core</artifactId>
    <version>3.8</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>info.cukes</groupId>
    <artifactId>cucumber-core</artifactId>
    <version>1.0.10</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>info.cukes</groupId>
    <artifactId>cucumber-java</artifactId>
    <version>1.0.10</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>info.cukes</groupId>
    <artifactId>cucumber-junit</artifactId>
    <version>1.0.10</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>info.cukes</groupId>
    <artifactId>cucumber-html</artifactId>
    <version>0.2.2</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>jbehave-junit-runner</artifactId>
    <version>1.0.2</version>
</dependency>
```

Now freaked file/folder structure with names that assure we'll trigger tests when needed.

{% img /blog/images/folder_structure.png %}

You may ask why cucumber folder structure is such a mess.
It's because this was the only way I was able to force Cucumber to use Unit Test Steps with Unit Tests and Integration Test Steps with IT Tests. This solutions allows you to have same steps with different implementation for Unit Tests and different for IT Tests.

Class implementation details will be a part of next post (Yep... before pom.xml post).

Impatient readers can take a look at GitHub...
