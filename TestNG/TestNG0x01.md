---
title:  testNG-HelloWorld
date: 2019-11-12
categories: 
- TestNG
tags: 
- TestNG
---



官网是这么介绍的testNG,n和g是取自NextGeneration的首字母所以你能感觉到这么牛逼之气了吧？

首先我们先来个helloworld去门

测试案例

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.yiibai</groupId>
    <artifactId>TestHelloWorld</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>TestHelloWorld</name>
    <url>http://maven.apache.org</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>6.8.7</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

javabean

```java
package com.jierutek.task;
public class RandomEmailGenerator {
    public String generator(){
        return "feedback@yiibai.com";
    }
}


package com.jierutek.task;
import org.junit.Assert;
import org.testng.annotations.Test;
public class TaskApplicationTests {
    @Test
    public void test(){
        RandomEmailGenerator randomEmailGenerator = new RandomEmailGenerator();
        String email = randomEmailGenerator.generator();
        Assert.assertNotNull(email);
        Assert.assertEquals(email,"feedback@yiibai.com");
    }
    
}

从字面上理解assertEqual()就是我确定参数一和参数二相等，我的断言对不对？，对就通过，不对就报错。答案很明显是通过
  C:\Users\Administrator\.IntelliJIdea2018.3\system\temp-testng-customsuite.xml
===============================================
Default Suite
Total tests run: 1, Failures: 0, Skips: 0
===============================================

Process finished with exit code 0
```

我看上面的测试类上面需要引入junit的Assert方法，但是我注释了哪个springtest依赖依然可以运行

