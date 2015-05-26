---
layout: post
title: "framework hell"
date: 2013-10-29 20:04:26 +0200
comments: true
categories:
  - groovy
  - java
  - junit
  - maven
  - pom
  - spock
  - surefire
  - testng
  - tutorial
---
In most cases setting test for a project end up quite quick, unit test classes have "Test" suffix, while integration tests have "IT" suffix.

But that's easy...

Today we'll show you how to use Java with JUnit, TestNG and Groovy with Spock tightly coupled with Maven within one project/module.

You may ask WHY??

!!!! BECAUSE F*** YOU THAT'S WHY !!!!

<!-- more -->

Why doing it the easy way when hard way is more fun !!!!!!!

First we need to distinguish tests on language level.
Easiest way is to keep them in dedicated folders


``` java
  /src/test/java/
  /src/test/groovy/
```


Now we need to distinguish tests by level and framework, best way is to do this by using suffixes and prefixes on classes.

HINT!!
Since Spock tests are handled by JUnit runner class you can treat those equally. What more... dividing tests based on framework will be done on Maven level so all we need to do is distinguish tests based on when they should be triggered within maven build lifecycle. Personally I like below convention.

``` java  
  SampleClassNameTest.java      <-  Java Unit Tests
  SampleClassNameIT.java        <-  Java Integration Tests
  SampleClassNameTest.groovy    <-  Groovy Unit Tests
  SampleClassNameSpec.groovy    <-  Groovy Integration Tests
```

Be careful with class names, since Groovy classes are compiled to same .class as Java classes and land in the same target location make sure you don't have same classes in Java and Groovy locations (if they are in different packages then it's fine). But most of IDE's will prompt you if such situation occurs.

And now last and simplest part of this setup... pom.xml

Hah, just kidding, this is the most tricky part.
But this will come in separate post in a few days.
