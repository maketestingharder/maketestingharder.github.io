---
layout: post
title: "testing without test environment"
date: 2014-06-17 21:42:07 +0200
comments: true
categories:
- github
- glassfish
- java
- jetty
- maven
- pom
- testng
- tomcat
- tutorial
---
It's always a problem... you develop a code, write unit tests, you build your project locally so unit tests can give you a quick feedback. What do you do if you need to test a web-app? You build it, run unit tests, everything seems to work fine, CI server builds your code, run tests and everything is ok... until you deploy your war/ear file into application server. Then it turns out it does not deploy, and your head blows, s**** and f*** are flying around...

<!-- more -->

Wouldn't it be nice if you wouldn't have to wait for whole CI/CD pipeline or manual deploy to be notified that your app can't be deployed or you can't communicate with it due to incorrect dependencies.

Turns out it's quite easy to do it. All you have to do is use embedded application server. I'll show you how to pick one, and set it according to your needs.

It always comes down to five phases/steps tightly coupled to maven life cycle.

There are few different maven plugins that can help you with it.


``` xml
<plugin>
    <groupId>org.eclipse.jetty</groupId>
    <artifactId>jetty-maven-plugin</artifactId>
    <version>9.0.0.M4</version>
</plugin>
```
``` xml
<plugin>
    <groupId>org.glassfish</groupId>
    <artifactId>maven-embedded-glassfish-plugin</artifactId>
    <version>3.1.1</version>
</plugin>
```
``` xml
<plugin>
    <groupId>org.apache.tomcat.maven</groupId>
    <artifactId>tomcat7-maven-plugin</artifactId>
    <version>2.0</version>
</plugin>
```
First one - Jetty - seems to be the most lightweight solution, if you don't use JMX (maybe there is a way to turn it on and configure it, but I wasn't able to figure it out) this one is for you. Setup is quick, easy and what's most important it does not take much time to set it up, run tests, tear down and get a report.

How to do this... will come next week. Stay tuned.
