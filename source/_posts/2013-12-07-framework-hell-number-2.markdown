---
layout: post
title: "framework hell #2"
date: 2013-12-07 21:34:51 +0200
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
Let's continue this -> [Framework Hell](/blog/2013/10/29/framework-hell/)

First step is simple, add basic dependencies.

<!-- more -->

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>maketestingharder</groupId>
    <artifactId>frameworkHell</artifactId>
    <version>666</version>

    <dependencies>
        <dependency>
            <groupId>org.spockframework</groupId>
            <artifactId>spock-core</artifactId>
            <version>0.7-groovy-2.0</version>
        </dependency>
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
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>6.8.5</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
        </dependency>
    </dependencies>
    <build>
     //
    </build>
</project>
```
Above section contains only basic dependencies, does not include parent section, which may contain common build steps shared between multiple child poms, does not contain dependency management section which could delegate dependency version management to other pom files (I guess this topic will have a separate entry as there are few ways to manage dependencies across multiple projects). For now we'll keep it simple (well.. maybe not exactly, since we'd like to share complex problems instead of simple ones :P).

Have you tried to build you're code? If you did, you have noticed that only testNG test were triggered. One more thing that you should notice is content of

``` java
target/test-classes/
```

All Java classes were compiled, and made it to that folder... What about Spock, Groovy to be precise? Java has no idea about Groovy but Groovy is one of JVM languages and Groovy compiler is able to compile Java so we'll use gmaven-plugin to compile those. To do this we have to fill our build section with below entries.

``` xml
<build>
    <plugins>
        <plugin>
            <groupId>org.codehaus.gmaven</groupId>
            <artifactId>gmaven-plugin</artifactId>
            <version>1.4</version>
            <executions>
                <execution>
                    <goals>
                        <goal>compile</goal>
                        <goal>testCompile</goal>
                    </goals>
                </execution>
            </executions>
            <dependencies>
                <dependency>
                    <groupId>org.codehaus.gmaven.runtime</groupId>
                    <artifactId>gmaven-runtime-2.0</artifactId>
                    <version>1.4</version>
                    <exclusions>
                        <exclusion>
                            <groupId>org.codehaus.groovy</groupId>
                            <artifactId>groovy-all</artifactId>
                        </exclusion>
                    </exclusions>
                </dependency>
                <dependency>
                    <groupId>org.codehaus.groovy</groupId>
                    <artifactId>groovy-all</artifactId>
                    <version>2.1.5</version>
                </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

Build you're code now... What happens? Still only TestNG entries on console... but target/test-classes now contains also groovy/spock classes. So we're half way there (well... 1/5 because we still have plenty to configure).

Now we have to setup test phases, so that unit test are triggered during test phase and IT tests in integration-tests phase. First one is simple. Just add this section.

``` xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.15</version>
        <configuration>
            <excludes>
                <exclude>**/*IT.java</exclude>
            </excludes>
        </configuration>
</plugin>
```

Why there is ".java" in this config? Because groovy and java classes are at binary level treated alike and surefire-plugin treats them also alike.
But this time instead of running more tests less were triggered. Where the hell are JUnit tests? In order to do this we need to setup two types of executions. One that will trigger TestNG and second that will kick off JUnit. So above section at the end should like like this.

``` xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.15</version>
    <configuration>
        <excludes>
            <exclude>**/*IT.java</exclude>
        </excludes>
    </configuration>
    <executions>
        <execution>
            <id>JUnitUnitTest</id>
            <phase>test</phase>
            <goals>
                <goal>test</goal>
            </goals>
            <configuration>
                <excludes>
                    <exclude>**/*IT.java</exclude>
                </excludes>
                <testNGArtifactName>none:none</testNGArtifactName>
            </configuration>
        </execution>
    </executions>
</plugin>
```

How about now? Surprised? I guess not... All we did is forcing test execution twice, first one with JUnit, second one with TestNG. And now we're half way there. As you've noticed only Unit tests were triggered. Now we need to add another plugin that will trigger Integration Tests. So we need to add below section.

``` xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-failsafe-plugin</artifactId>
    <version>2.16</version>
    <dependencies>
        <dependency>
            <groupId>org.apache.maven.surefire</groupId>
            <artifactId>surefire-testng</artifactId>
            <version>2.16</version>
        </dependency>
        <dependency>
            <groupId>org.apache.maven.surefire</groupId>
            <artifactId>surefire-junit47</artifactId>
            <version>2.16</version>
        </dependency>
    </dependencies>
    <executions>
        <execution>
            <id>TestNGIT</id>
            <phase>integration-test</phase>
            <goals>
                <goal>integration-test</goal>
            </goals>
            <configuration>
                <threadCount>1</threadCount>
            </configuration>
        </execution>
    </executions>
</plugin>
```

Last section does basically the same as previous one, but is triggered in integration test phase.

Of course there are some drawbacks... I have no idea how to separate properly IT tests, for some reason failsafe-plugin does not work similarly to surefire-plugin that's why there are so many print lines despite the fact that those tests are not triggered (take a look at TestSummary for integration-test phase...).

Next time I'll show you how to test Java web apps/services (*.war) without deploying them into test environment.
