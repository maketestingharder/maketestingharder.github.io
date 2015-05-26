---
layout: post
title: "testing without test environment - jetty"
date: 2014-07-10 21:44:39 +0200
comments: true
categories:
- IT tests
- java
- jetty
- maven
- pom
---
So how it actually goes?
And the answer is, as always in IT world, it depends...

For Jetty it's pretty simple.

``` java
start Jetty -> deploy war -> run tests -> stop Jetty
```
<!-- more -->

``` xml
<plugin>
    <groupId>org.eclipse.jetty</groupId>
    <artifactId>jetty-maven-plugin</artifactId>
    <version>9.0.0.M4</version>
    <configuration>
        <war>${project.basedir}/webapp/frameworkHell.war</war>
        <webAppConfig>
            <contextPath>/frameworkHell</contextPath>
        </webAppConfig>
    </configuration>
    <executions>
        <execution>
            <id>start-jetty</id>
            <phase>pre-integration-test</phase>
            <goals>
                <goal>run-war</goal>
            </goals>
            <configuration>
                <scanIntervalSeconds>0</scanIntervalSeconds>
                <daemon>true</daemon>
            </configuration>
        </execution>
        <execution>
            <id>stop-jetty</id>
            <phase>post-integration-test</phase>
            <goals>
                <goal>stop</goal>
            </goals>
            <configuration>
                <stopKey>stopJetty</stopKey>
                <stopPort>9966</stopPort>
            </configuration>
        </execution>
    </executions>
</plugin>
```

In order to do so we need to make sure that our application is build prior to test execution.
This is one of drawbacks, but main advantage is that if those tests will fail, main build does not.
All you need to do is add one more plugin (yep, one more!!) to your build section in pom file.

Above section does all you need. All you have to do is to properly set path to your war file, and decide under what context path your app will be available.

Just in case you'd like to test it and you don't have war file you can use, take a look at my github, there's a simple web app that does nothing, it's a "hello world" example.

Why Jetty?
Because it's light, easy to use, and in most cases will be enough.
Sure, you can use Tomcat (will show next time how to do this) but do you really need it? It's quite heavy, and configuration sometimes can give you headache.
So if you don't use any fancy stuff like JMX, Jetty will do just best.

Next time GlassFish... heavy server... but worth trying.
