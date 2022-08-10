---
title: POM 深入与强化
top: false
cover: false
toc: true
mathjax: true
tags:
  - maven
categories:
  - 工作
typora-root-url: ../
date: 2022-08-09 11:00:00
password:
summary:
---





# 第一节 重新认识Maven

## 1、Maven 的完整功能

在入门的时候我们介绍说 Maven 是一款『**构建**管理』和『**依赖**管理』的工具。但事实上这只是 Maven 的一部分功能。Maven 本身的产品定位是一款『**项目**管理工具』。

## 2、项目管理功能的具体体现

下面是 spring-boot-starter 的 POM 文件，可以看到：除了我们熟悉的坐标标签、dependencies 标签，还有 description、url、organization、licenses、developers、scm、issueManagement 等这些描述项目信息的标签。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <!-- This module was also published with a richer model, Gradle metadata,  -->
  <!-- which should be used instead. Do not delete the following line which  -->
  <!-- is to indicate to Gradle or any Gradle module metadata file consumer  -->
  <!-- that they should prefer consuming it instead. -->
  <!-- do_not_remove: published-with-gradle-metadata -->
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
  <version>2.5.6</version>
  <name>spring-boot-starter</name>
  <description>Core starter, including auto-configuration support, logging and YAML</description>
  <url>https://spring.io/projects/spring-boot</url>
  <organization>
    <name>Pivotal Software, Inc.</name>
    <url>https://spring.io</url>
  </organization>
  <licenses>
    <license>
      <name>Apache License, Version 2.0</name>
      <url>https://www.apache.org/licenses/LICENSE-2.0</url>
    </license>
  </licenses>
  <developers>
    <developer>
      <name>Pivotal</name>
      <email>info@pivotal.io</email>
      <organization>Pivotal Software, Inc.</organization>
      <organizationUrl>https://www.spring.io</organizationUrl>
    </developer>
  </developers>
  <scm>
    <connection>scm:git:git://github.com/spring-projects/spring-boot.git</connection>
    <developerConnection>scm:git:ssh://git@github.com/spring-projects/spring-boot.git</developerConnection>
    <url>https://github.com/spring-projects/spring-boot</url>
  </scm>
  <issueManagement>
    <system>GitHub</system>
    <url>https://github.com/spring-projects/spring-boot/issues</url>
  </issueManagement>

  <dependencies>
    <dependency>
      ……
    </dependency>
  </dependencies>
</project>
```



所以从『项目管理』的角度来看，Maven 提供了如下这些功能：

- 项目对象模型（POM）：将整个项目本身抽象、封装为应用程序中的一个对象，以便于管理和操作。
- 全局性构建逻辑重用：Maven 对整个构建过程进行封装之后，程序员只需要指定配置信息即可完成构建。让构建过程从 Ant 的『编程式』升级到了 Maven 的『声明式』。
- 构件的标准集合：在 Maven 提供的标准框架体系内，所有的构件都可以按照统一的规范生成和使用。
- 构件关系定义：Maven 定义了构件之间的三种基本关系，让大型应用系统可以使用 Maven 来进行管理
  - 继承关系：通过从上到下的继承关系，将各个子构件中的重复信息提取到父构件中统一管理
  - 聚合关系：将多个构件聚合为一个整体，便于统一操作
  - 依赖关系：Maven 定义了依赖的范围、依赖的传递、依赖的排除、版本仲裁机制等一系列规范和标准，让大型项目可以有序容纳数百甚至更多依赖
- 插件目标系统：Maven 核心程序定义抽象的生命周期，然后将插件的目标绑定到生命周期中的特定阶段，实现了标准和具体实现解耦合，让 Maven 程序极具扩展性
- 项目描述信息的维护：我们不仅可以在 POM 中声明项目描述信息，更可以将整个项目相关信息收集起来生成 HTML 页面组成的一个可以直接访问的站点。这些项目描述信息包括：
  - 公司或组织信息
  - 项目许可证
  - 开发成员信息
  - issue 管理信息
  - SCM 信息





# 第二节 POM 的四个层次

## 1、超级 POM

经过我们前面的学习，我们看到 Maven 在构建过程中有很多默认的设定。例如：源文件存放的目录、测试源文件存放的目录、构建输出的目录……等等。但是其实这些要素也都是被 Maven 定义过的。定义的位置就是：**超级 POM**。

关于超级 POM，Maven 官网是这样介绍的：

> The Super POM is Maven's default POM. All POMs extend the Super POM unless explicitly set, meaning the configuration specified in the Super POM is inherited by the POMs you created for your projects.
>
> 译文：Super POM 是 Maven 的默认 POM。除非明确设置，否则所有 POM 都扩展 Super POM，这意味着 Super POM 中指定的配置由您为项目创建的 POM 继承。

所以我们自己的 POM 即使没有明确指定一个父工程（父 POM），其实也默认继承了超级 POM。就好比一个 Java 类默认继承了 Object 类。

那么超级 POM 中定义了哪些东西呢？点击[这里](http://heavy_code_industry.gitee.io/code_heavy_industry/pro002-maven/chapter09/super-pom-content.html)查看。

## 2、父 POM

和 Java 类一样，POM 之间其实也是**单继承**的。如果我们给一个 POM 指定了父 POM，那么继承关系如下图所示：

![images](data:image/png;base64,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)

## 3、有效 POM

### ①概念

有效 POM 英文翻译为 effective POM，它的概念是这样的——在 POM 的继承关系中，子 POM 可以覆盖父 POM 中的配置；如果子 POM 没有覆盖，那么父 POM 中的配置将会被继承。按照这个规则，继承关系中的所有 POM 叠加到一起，就得到了一个最终生效的 POM。显然 Maven 实际运行过程中，执行构建操作就是按照这个最终生效的 POM 来运行的。这个最终生效的 POM 就是**有效 POM**，英文叫**effective POM**。

### ②查看有效 POM

> mvn help:effective-pom

运行效果[点击这里](http://heavy_code_industry.gitee.io/code_heavy_industry/pro002-maven/chapter09/effective-pom-content)查看。

## 4、小结

综上所述，平时我们使用和配置的 POM 其实大致是由四个层次组成的：

- 超级 POM：所有 POM 默认继承，只是有直接和间接之分。
- 父 POM：这一层可能没有，可能有一层，也可能有很多层。
- 当前 pom.xml 配置的 POM：我们最多关注和最多使用的一层。
- 有效 POM：隐含的一层，但是实际上真正生效的一层。



# 第三节 属性的声明与引用

## 1、help 插件的各个目标

官网说明地址：https://maven.apache.org/plugins/maven-help-plugin

| 目标                    | 说明                                              |
| ----------------------- | ------------------------------------------------- |
| help:active-profiles    | 列出当前已激活的 profile                          |
| help:all-profiles       | 列出当前工程所有可用 profile                      |
| help:describe           | 描述一个插件和/或 Mojo 的属性                     |
| help:effective-pom      | 以 XML 格式展示有效 POM                           |
| help:effective-settings | 为当前工程以 XML 格式展示计算得到的 settings 配置 |
| **help:evaluate**       | 计算用户在交互模式下给出的 Maven 表达式           |
| help:system             | 显示平台详细信息列表，如系统属性和环境变量        |

## 2、使用 help:evaluate 查看属性值

### ①定义属性

```xml
<properties>
    <com.atguigu.hello>good morning maven</com.atguigu.hello>
</properties>
```



### ②运行命令

![images](data:image/png;base64,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)

### ③运行结果

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img003.4bc5a83a.png)

## 3、通过 Maven 访问系统属性

### ① Java 系统属性一览

#### [1] Java 代码

```java
Properties properties = System.getProperties();
Set<Object> propNameSet = properties.keySet();
for (Object propName : propNameSet) {
    String propValue = properties.getProperty((String) propName);
    System.out.println(propName + " = " + propValue);
}
```



#### [2]运行结果

> java.runtime.name = Java(TM) SE Runtime Environment
> sun.boot.library.path = D:\software\Java\jre\bin
> java.vm.version = 25.141-b15
> java.vm.vendor = Oracle Corporation
> java.vendor.url = http://java.oracle.com/
> path.separator = ;
> java.vm.name = Java HotSpot(TM) 64-Bit Server VM
> file.encoding.pkg = sun.io
> user.country = CN
> user.script =
> sun.java.launcher = SUN_STANDARD
> sun.os.patch.level =
> java.vm.specification.name = Java Virtual Machine Specification
> user.dir = D:\idea2019workspace\atguigu-maven-test-prepare
> java.runtime.version = 1.8.0_141-b15
> java.awt.graphicsenv = sun.awt.Win32GraphicsEnvironment
> java.endorsed.dirs = D:\software\Java\jre\lib\endorsed
> os.arch = amd64
> java.io.tmpdir = C:\Users\ADMINI~1\AppData\Local\Temp\
> line.separator =
> java.vm.specification.vendor = Oracle Corporation
> user.variant =
> os.name = Windows 10
> sun.jnu.encoding = GBK
> java.library.path = D:\software\Java\bin;C:\WINDOWS\Sun\Java\bin;C:\WIN……
> java.specification.name = Java Platform API Specification
> java.class.version = 52.0
> sun.management.compiler = HotSpot 64-Bit Tiered Compilers
> os.version = 10.0
> user.home = C:\Users\Administrator
> user.timezone =
> java.awt.printerjob = sun.awt.windows.WPrinterJob
> file.encoding = UTF-8
> java.specification.version = 1.8
> java.class.path = D:\software\Java\jre\lib\charsets.jar;D:\softw……
> user.name = Administrator
> java.vm.specification.version = 1.8
> sun.java.command = com.atguigu.maven.MyTest
> java.home = D:\software\Java\jre
> sun.arch.data.model = 64
> user.language = zh
> java.specification.vendor = Oracle Corporation
> awt.toolkit = sun.awt.windows.WToolkit
> java.vm.info = mixed mode
> java.version = 1.8.0_141
> java.ext.dirs = D:\software\Java\jre\lib\ext;C:\WINDOWS\Sun\Java\lib\ext
> sun.boot.class.path = D:\software\Java\jre\lib\resources.jar;D:\sof……
> java.vendor = Oracle Corporation
> file.separator = \
> java.vendor.url.bug = http://bugreport.sun.com/bugreport/
> sun.io.unicode.encoding = UnicodeLittle
> sun.cpu.endian = little
> sun.desktop = windows
> sun.cpu.isalist = amd64

### ②使用 Maven 访问系统属性

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img004.f6eca629.png)

## 4、访问系统环境变量

${env.系统环境变量名}

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img009.4a2e24e8.png)

## 5、访问 project 属性

### ①含义

使用表达式 ${project.xxx} 可以访问当前 POM 中的元素值。

### ②访问一级标签

${project.标签名}

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img005.fba68730.png)

### ③访问子标签

${project.标签名.子标签名}

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img006.265f7fd8.png)

### ④访问列表标签

${project.标签名[下标]}

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img007.e3598dca.png)

## 6、访问 settings 全局配置

${settings.标签名} 可以访问 settings.xml 中配置的元素值。

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img008.5ad4fbe3.png)

## 7、用途

- 在当前 pom.xml 文件中引用属性
- 资源过滤功能：在非 Maven 配置文件中引用属性，由 Maven 在处理资源时将引用属性的表达式替换为属性值





# 第四节 build 标签详解

## 1、一睹真容

在实际使用 Maven 的过程中，我们会发现 build 标签有时候有，有时候没，这是怎么回事呢？其实通过有效 POM 我们能够看到，build 标签的相关配置其实一直都在，只是在我们需要定制构建过程的时候才会通过配置 build 标签覆盖默认值或补充配置。这一点我们可以通过打印有效 POM 来看到。

[点我查看 build 标签的完整示例](http://heavy_code_industry.gitee.io/code_heavy_industry/pro002-maven/chapter09/whole-build.html)

所以**本质**上来说：我们配置的 build 标签都是对**超级 POM 配置**的**叠加**。那我们又为什么要在默认配置的基础上叠加呢？很简单，在默认配置无法满足需求的时候**定制构建过程**。

## 2、build 标签组成

从完整示例中我们能够看到，build 标签的子标签大致包含三个主体部分：

### ①定义约定的目录结构

参考示例中的如下部分：

```xml
<sourceDirectory>D:\idea2019workspace\atguigu-maven-test-prepare\src\main\java</sourceDirectory>
<scriptSourceDirectory>D:\idea2019workspace\atguigu-maven-test-prepare\src\main\scripts</scriptSourceDirectory>
<testSourceDirectory>D:\idea2019workspace\atguigu-maven-test-prepare\src\test\java</testSourceDirectory>
<outputDirectory>D:\idea2019workspace\atguigu-maven-test-prepare\target\classes</outputDirectory>
<testOutputDirectory>D:\idea2019workspace\atguigu-maven-test-prepare\target\test-classes</testOutputDirectory>
<resources>
    <resource>
        <directory>D:\idea2019workspace\atguigu-maven-test-prepare\src\main\resources</directory>
    </resource>
</resources>
<testResources>
    <testResource>
        <directory>D:\idea2019workspace\atguigu-maven-test-prepare\src\test\resources</directory>
    </testResource>
</testResources>
<directory>D:\idea2019workspace\atguigu-maven-test-prepare\target</directory>
```



我们能看到各个目录的作用如下：

| 目录名                | 作用                       |
| --------------------- | -------------------------- |
| sourceDirectory       | 主体源程序存放目录         |
| scriptSourceDirectory | 脚本源程序存放目录         |
| testSourceDirectory   | 测试源程序存放目录         |
| outputDirectory       | 主体源程序编译结果输出目录 |
| testOutputDirectory   | 测试源程序编译结果输出目录 |
| resources             | 主体资源文件存放目录       |
| testResources         | 测试资源文件存放目录       |
| directory             | 构建结果输出目录           |

### ②备用插件管理

pluginManagement 标签存放着几个极少用到的插件：

- maven-antrun-plugin
- maven-assembly-plugin
- maven-dependency-plugin
- maven-release-plugin

通过 pluginManagement 标签管理起来的插件就像 dependencyManagement 一样，子工程使用时可以省略版本号，起到在父工程中统一管理版本的效果。情看下面例子：

- 被 spring-boot-dependencies 管理的插件信息：

```xml
<plugin>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-maven-plugin</artifactId>
	<version>2.6.2</version>
</plugin>
```

- 子工程使用的插件信息：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

### ③生命周期插件

plugins 标签存放的是默认生命周期中实际会用到的插件，这些插件想必大家都不陌生，所以抛开插件本身不谈，我们来看看 plugin 标签的结构：

```xml
<plugin>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.1</version>
    <executions>
        <execution>
            <id>default-compile</id>
            <phase>compile</phase>
            <goals>
                <goal>compile</goal>
            </goals>
        </execution>
        <execution>
            <id>default-testCompile</id>
            <phase>test-compile</phase>
            <goals>
                <goal>testCompile</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```



#### [1]坐标部分

artifactId 和 version 标签定义了插件的坐标，作为 Maven 的自带插件这里省略了 groupId。

#### [2]执行部分

executions 标签内可以配置多个 execution 标签，execution 标签内：

- id：指定唯一标识
- phase：关联的生命周期阶段
- goals/goal：关联指定生命周期的目标
  - goals 标签中可以配置多个 goal 标签，表示一个生命周期环节可以对应当前插件的多个目标。

另外，插件目标的执行过程可以进行配置，例如 maven-site-plugin 插件的 site 目标：

```xml
<execution>
    <id>default-site</id>
    <phase>site</phase>
    <goals>
        <goal>site</goal>
    </goals>
    <configuration>
        <outputDirectory>D:\idea2019workspace\atguigu-maven-test-prepare\target\site</outputDirectory>
        <reportPlugins>
            <reportPlugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-project-info-reports-plugin</artifactId>
            </reportPlugin>
        </reportPlugins>
    </configuration>
</execution>
```



configuration 标签内进行配置时使用的标签是插件本身定义的。就以 maven-site-plugin 插件为例，它的核心类是 org.apache.maven.plugins.site.render.SiteMojo，在这个类中我们看到了 outputDirectory 属性：

![images](data:image/png;base64,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)

SiteMojo 的父类是：AbstractSiteRenderingMojo，在父类中我们看到 reportPlugins 属性：

![images](data:image/png;base64,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)

**结论**：每个插件能够做哪些设置都是各个插件自己规定的，无法一概而论。

## 3、典型应用：指定 JDK 版本

### ①提出问题

前面我们在 settings.xml 中配置了 JDK 版本，那么将来把 Maven 工程部署都服务器上，脱离了 settings.xml 配置，如何保证程序正常运行呢？思路就是我们直接把 JDK 版本信息告诉负责编译操作的 maven-compiler-plugin 插件，让它在构建过程中，按照我们指定的信息工作。

### ②暂时取消 settings.xml 配置

为了测试对 maven-compiler-plugin 插件进行配置的效果，我们暂时取消 settings.xml 中的 profile 配置。

```xml
<!-- 配置Maven工程的默认JDK版本 -->
<!-- <profile>
  <id>jdk-1.8</id>
  <activation>
	<activeByDefault>true</activeByDefault>
	<jdk>1.8</jdk>
  </activation>
  <properties>
	<maven.compiler.source>1.8</maven.compiler.source>
	<maven.compiler.target>1.8</maven.compiler.target>
	<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
  </properties>
</profile> -->
```



### ③编写源文件代码

很明显这里用到了 Lambda 表达式，这是 JDK 1.8 才支持的语法。

```java
package com.atguigu.maven;

public class Hello {

    public void hello() {
        new Thread(()->{
            System.out.println("thread ...");
        }).start();
    }

}
```

此时我们执行编译命令：

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img116.a4961940.png)

### ④配置构建过程

```xml
<!-- build 标签：意思是告诉 Maven，你的构建行为，我要开始定制了！ -->
<build>
    <!-- plugins 标签：Maven 你给我听好了，你给我构建的时候要用到这些插件！ -->
    <plugins>
        <!-- plugin 标签：这是我要指定的一个具体的插件 -->
        <plugin>
            <!-- 插件的坐标。此处引用的 maven-compiler-plugin 插件不是第三方的，是一个 Maven 自带的插件。 -->
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            
            <!-- configuration 标签：配置 maven-compiler-plugin 插件 -->
            <configuration>
                <!-- 具体配置信息会因为插件不同、需求不同而有所差异 -->
                <source>1.8</source>
                <target>1.8</target>
                <encoding>UTF-8</encoding>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### ⑤再次执行编译命令

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img117.567b90ed.png)

### ⑥两种配置方式比较

- settings.xml 中配置：仅在本地生效，如果脱离当前 settings.xml 能够覆盖的范围，则无法生效。
- 在当前 Maven 工程 pom.xml 中配置：无论在哪个环境执行编译等构建操作都有效。

### ⑦补充说明

#### [1]source 标签含义

查看 [Maven 官网页面 (opens new window)](http://maven.apache.org/plugins/maven-compiler-plugin/compile-mojo.html)，我们找到 source 标签的介绍：

![images](data:image/png;base64,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)

翻译过来就是：调用 Java 编译器命令时传入的 -source 参数。那对编译器来说，-source 参数是啥意思呢？

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img109.6a115e94.png)



『提供与指定发行版的源兼容性』这句话我的理解是：

- 我们写代码是按 JDK 1.8 写的——这就是『源兼容性』里的『源』。
- 指定发行版就是我们指定的 JDK 1.8。
- 『兼容性』是谁和谁兼容呢？现在源代码是既定的，所以就是要求编译器使用指定的 JDK 版本来兼容我们的源代码。



另外我们还看到：

![images](data:image/png;base64,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)

这个功能还可以通过在 properties 标签中配置 maven.compiler.source 属性来实现。所以我们也经常会看到类似这样的配置：

```xml
<properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
```



#### [2]target 标签含义

![images](data:image/png;base64,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)

翻译过来就是：调用 Java 编译器命令时传入的 -target 参数。那对编译器来说，-target 参数是啥意思呢？

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img112.f7844c6b.png)

『生成特定 VM 版本的类文件』这句话我的理解是：

- VM 指 JVM
- 类文件指 *.class 字节码文件
- 整体意思就是源文件编译后，生成的 *.class 字节码文件要符合指定的 JVM 版本

## 4、典型应用：SpringBoot 定制化打包

### ①需求

很显然 spring-boot-maven-plugin 并不是 Maven 自带的插件，而是 SpringBoot 提供的，用来改变 Maven 默认的构建行为。具体来说是改变打包的行为。默认情况下 Maven 调用 maven-jar-plugin 插件的 jar 目标，生成普通的 jar 包。

普通 jar 包没法使用 java -jar xxx.jar 这样的命令来启动、运行，但是 SpringBoot 的设计理念就是每一个『微服务』导出为一个 jar 包，这个 jar 包可以使用 java -jar xxx.jar 这样的命令直接启动运行。

这样一来，打包的方式肯定要进行调整。所以 SpringBoot 提供了 spring-boot-maven-plugin 这个插件来定制打包行为。

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img118.48c7b12c.png)

### ②示例代码

所有的一切已经都被 SpringBoot 封装好了，所以配置非常简单，提供插件坐标即可。

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<version>2.5.5</version>
		</plugin>
	</plugins>
</build>
```



### ③插件的七个目标

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img120.3be3c586.png)

| 目标名称                | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| spring-boot:build-image | Package an application into a OCI image using a buildpack.   |
| spring-boot:build-info  | Generate a build-info.properties file based on the content of the current MavenProject. |
| spring-boot:help        | Display help information on spring-boot-maven-plugin. Call mvn spring-boot:help -Ddetail=true -Dgoal=<goal-name> to display parameter details. |
| spring-boot:repackage   | Repackage existing JAR and WAR archives so that they can be executed from the command line using java -jar. With layout=NONE can also be used simply to package a JAR with nested dependencies (and no main class, so not executable). |
| spring-boot:run         | Run an application in place.                                 |
| spring-boot:start       | Start a spring application. Contrary to the run goal, this does not block and allows other goals to operate on the application. This goal is typically used in integration test scenario where the application is started before a test suite and stopped after. |
| spring-boot:stop        | Stop an application that has been started by the 'start' goal. Typically invoked once a test suite has completed. |

## 5、典型应用：Mybatis 逆向工程

使用 Mybatis 的逆向工程需要使用如下配置，MBG 插件的特点是需要提供插件所需的依赖：

```xml
<!-- 控制 Maven 在构建过程中相关配置 -->
<build>
		
	<!-- 构建过程中用到的插件 -->
	<plugins>
		
		<!-- 具体插件，逆向工程的操作是以构建过程中插件形式出现的 -->
		<plugin>
			<groupId>org.mybatis.generator</groupId>
			<artifactId>mybatis-generator-maven-plugin</artifactId>
			<version>1.3.0</version>
	
			<!-- 插件的依赖 -->
			<dependencies>
				
				<!-- 逆向工程的核心依赖 -->
				<dependency>
					<groupId>org.mybatis.generator</groupId>
					<artifactId>mybatis-generator-core</artifactId>
					<version>1.3.2</version>
				</dependency>
					
				<!-- 数据库连接池 -->
				<dependency>
					<groupId>com.mchange</groupId>
					<artifactId>c3p0</artifactId>
					<version>0.9.2</version>
				</dependency>
					
				<!-- MySQL驱动 -->
				<dependency>
					<groupId>mysql</groupId>
					<artifactId>mysql-connector-java</artifactId>
					<version>5.1.8</version>
				</dependency>
			</dependencies>
		</plugin>
	</plugins>
</build>
```

## 6、小结

不知大家有没有发现，通常需要用到 build 标签的时候底层都会帮我们封装好，需要我们配置的地方不多。即使有些地方需要我们配置，也不会真的我们自己去写，把现成的案例复制过来就行。

所以对 build 标签来说，我们的掌握要求就是：**能大致看懂**就行。



# 第五节 依赖配置补充

TIP

[Maven 官网介绍依赖机制(opens new window)](https://maven.apache.org/guides/introduction/introduction-to-dependency-mechanism.html)

## 1、依赖范围

### ①import

管理依赖最基本的办法是继承父工程，但是和 Java 类一样，Maven 也是单继承的。如果不同体系的依赖信息封装在不同 POM 中了，没办法继承多个父工程怎么办？这时就可以使用 import 依赖范围。

典型案例当然是在项目中引入 SpringBoot、SpringCloud 依赖：

```xml
<dependencyManagement>
    <dependencies>

        <!-- SpringCloud 依赖导入 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Hoxton.SR9</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <!-- SpringCloud Alibaba 依赖导入 -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.2.6.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <!-- SpringBoot 依赖导入 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.3.6.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```



import 依赖范围使用要求：

- 打包类型必须是 pom
- 必须放在 dependencyManagement 中

> 官网说明如下：
>
> This scope is only supported on a dependency of type `pom` in the `<dependencyManagement>` section. It indicates the dependency is to be replaced with the effective list of dependencies in the specified POM's `<dependencyManagement>` section. Since they are replaced, dependencies with a scope of `import` do not actually participate in limiting the transitivity of a dependency.

### ②system

以 Windows 系统环境下开发为例，假设现在 D:\tempare\atguigu-maven-test-aaa-1.0-SNAPSHOT.jar 想要引入到我们的项目中，此时我们就可以将依赖配置为 system 范围：

```xml
<dependency>
    <groupId>com.atguigu.maven</groupId>
    <artifactId>atguigu-maven-test-aaa</artifactId>
    <version>1.0-SNAPSHOT</version>
    <systemPath>D:\tempare\atguigu-maven-test-aaa-1.0-SNAPSHOT.jar</systemPath>
    <scope>system</scope>
</dependency>
```



但是很明显：这样引入依赖完全不具有可移植性，所以**不要使用**。如果需要引入体系外 jar 包我们后面会讲专门的办法。

### ③runtime

专门用于编译时不需要，但是运行时需要的 jar 包。比如：编译时我们根据接口调用方法，但是实际运行时需要的是接口的实现类。典型案例是：

```xml
<!--热部署 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```



## 2、可选依赖

### ①配置举例

```xml
<!--热部署 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```



### ②本质含义

可选其实就是『可有可无』。官网的解释是：

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img013.b6ada9b0.png)

其核心含义是：Project X 依赖 Project A，A 中一部分 X 用不到的代码依赖了 B，那么对 X 来说 B 就是『可有可无』的。

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img012.b802c22a.png)

## 3、版本仲裁

### ①最短路径优先

在下图的例子中，对模块 pro25-module-a 来说，Maven 会采纳 1.2.12 版本。

![images](data:image/png;base64,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)

### ②路径相同时先声明者优先

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img205.1dbd09c7.png)

此时 Maven 采纳哪个版本，取决于在 pro29-module-x 中，对 pro30-module-y 和 pro31-module-z 两个模块的依赖哪一个先声明。

### ③小结

其实 Maven 的版本仲裁机制只是在没有人为干预的情况下，自主决定 jar 包版本的一个办法。而实际上我们要使用具体的哪一个版本，还要取决于项目中的实际情况。所以在项目正常运行的情况下，jar 包版本可以由 Maven 仲裁，不必我们操心；而发生冲突时 Maven 仲裁决定的版本无法满足要求，此时就应该由程序员明确指定 jar 包版本。





# 第六节 Maven 自定义插件

## 1、本节定位

其实实际开发中几乎没有什么场景需要我们开发自定义 Maven 插件，所以本节只是通过这个角度帮助我们更好的理解插件的目标和生命周期阶段之间的关系。

## 2、插件开发

### ①创建工程

[略]

### ②设定打包方式

```xml
<packaging>maven-plugin</packaging>
```



### ③引入依赖

下面两种方式二选一：

#### [1]将来在文档注释中使用注解

```xml
<dependency>
    <groupId>org.apache.maven</groupId>
    <artifactId>maven-plugin-api</artifactId>
    <version>3.5.2</version>
</dependency>
```



#### [2]将来直接使用注解

```xml
<dependency>
    <groupId>org.apache.maven.plugin-tools</groupId>
    <artifactId>maven-plugin-annotations</artifactId>
    <version>3.5.2</version>
</dependency>
```



### ④创建 Mojo 类

Mojo 类是一个 Maven 插件的核心类。

Mojo 这个单词的意思是：Maven Old Java Object，其实 mojo 这个单词本身包含魔力;符咒(袋);护身符;(人的)魅力的含义，Maven 用 Mojo 是因为它是对 POJO 开的一个小玩笑。

#### [1] Mojo 接口

每一个 Mojo 都需要实现 org.apache.maven.plugin.Mojo 接口。

![images](data:image/png;base64,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)

#### [2] AbstractMojo 抽象类

我们实现 Mojo 接口比较困难，幸好可以继承 AbstractMojo，此时我们只要实现 execute() 这一个方法即可。

```java
public class MyHelloPlugin extends AbstractMojo {
    @Override
    public void execute() throws MojoExecutionException, MojoFailureException {
        getLog().info("---> This is my first maven plugin. <---");
    }
}
```



## 3、插件配置

### ①Mojo 类中的配置

#### [1]文档注释中用注解

对应的 pom.xml 中的依赖： maven-plugin-api

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img015.ca57432a.png)

#### [2]直接在类上标记注解

对应 pom.xml 中的依赖：maven-plugin-annotations

```java
// name 属性：指定目标名称
@Mojo(name = "firstBlood")
public class MyPluginOfFistBlood extends AbstractMojo {
    @Override
    public void execute() throws MojoExecutionException, MojoFailureException {
        getLog().info("---> first blood <---");
    }
}
```



### ②安装插件

要在后续使用插件，就必须至少将插件安装到本地仓库。

### ③注册插件

我们需要将插件坐标中的 groupId 部分注册到 **settings.xml** 中。

```xml
<pluginGroups>
	<!-- pluginGroup
	 | Specifies a further group identifier to use for plugin lookup.
	<pluginGroup>com.your.plugins</pluginGroup>
	-->
	<pluginGroup>com.atguigu.maven</pluginGroup>
</pluginGroups>
```



## 4、使用插件

### ①识别插件前缀

Maven 根据插件的 artifactId 来识别插件前缀。例如下面两种情况：

#### [1]前置匹配

- 匹配规则：${prefix}-maven-plugin
- artifactId：hello-maven-plugin
- 前缀：hello

#### [2]中间匹配

- 匹配规则：maven-${prefix}-plugin
- artifactId：maven-good-plugin
- 前缀：good

### ②在命令行直接用

- 命令：

```sh
mvn hello:sayHello
```



- 效果：

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img016.faea8444.png)

### ③配置到 build 标签里

这里找一个和插件无关的 Maven 工程配置才有说服力。

#### [1]配置

```xml
<build>
	<plugins>
		<plugin>
			<groupId>com.atguigu.maven</groupId>
			<artifactId>hello-maven-plugin</artifactId>
			<version>1.0-SNAPSHOT</version>
			<executions>
				<execution>
                    <id>hello</id>
                    <!-- 指定和目标关联的生命周期阶段 -->
					<phase>clean</phase>
					<goals>
						<goal>sayHello</goal>
					</goals>
				</execution>
                <execution>
                    <id>blood</id>
                    <phase>validate</phase>
                    <goals>
                        <goal>firstBlood</goal>
                    </goals>
                </execution>
			</executions>
		</plugin>
	</plugins>
</build>
```



#### [2]效果

![images](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAANsAAAExCAIAAAB6SUBHAAAgbklEQVR42u2dfXAUZZ7HnyS8hZjlJWQQjlclEF6C6+nhoYgbFKtCYekucLBXV4dVt4bS1TqhVssq1xML77w/VlJbpbtF0KrFK+ugENEyJrVyEmV348IJciTImIT1Qi4rTBJJCAmsQHL9MtP9PN1P93Q/8/T0b2Z+X6qomZ7nefrp7k+el+7ft5+8kZERgkKBUR4SiQIlJBIFS25Enjn7vwtvnXPwo0/pjT988L6w64zKZjkSqeAYPduh8JcBRDZsydtR3tq0tUz90lZz9/xtn5Hq+pFdVamVppa0f0OiXJRXmedNOZXvPeLzOvCJ1HEkGn+ZRaR6NqIviMLIliZOpFLEmlr1w/Kdlj8UXeJ/Lpkg6UQaOBJxIiWA4UMUkUxzmXJpQqJAVskkOn0SKgZa3Csur42khUT6E3Po5heRi5NJCpJIC4IWcYikOiStOzL6LKPXMrdoG4hW+3qyZk2tLYHRpbGY2ZsaOku83HZzU/WHreUvG6eIAWNH+c6KbdtqCd2n8kor85Fr+U7l1/3l9p4qUV9CddoufbblRJUR3pmJl1q/Yf8atUh1K4mnonK5JyBuJXOO1IE97fPPRx55n7riX+0hj6bwZ5g6kW01W+rW7tIqbpJD11rd2swcm44wfRaoBIlC5lE83L2jmWzYo19u7QK3m4yy+U1ibScucY7W1FYbdTSGO7zSfOTSjoiYf202IssIffD80an9RPHPTLw+Oi86V9WWs+4pgWPJ1iNNQuQLliueWscgo420t5J0/TidINvCWxMkfk0gSbbcHf3ZC9HN0Z81ra27ezPZowFJZ+H22o5EGjmVra6lieXit5H0VaWTUhOgdltSpzNTRf/A/ZwsQbuXkhNHaqknCY9IT0NGs3Uo49dPhEj9ROgfKBZf2LB/B9ljOeEAiWRvRfFGV7yBaoMXIjfbjl8WkfaSjY32Kx4eka39321p+ubOKYVdQ9eGrg+/+P3S984NNF24smFucfWCSZazpsFZwe21E71bQ03NvK2cno3tQagCN+8nn1WoSfXPet/Nlkn/TViIjHc6VMUc2wBOae5EOuXi/zW2tbWVlZXZj9aCpOVEtTucmZSJLGvwULILkfYTmy4i6zovv9rSqxA5Jp80xa5ECketiBQe7BhYNHHsa8tvNiqo99jLq6tJLTFrpWw1pwh6ElufTl0M2yibYdw6rKHnAMasgljPqL5bqmIOZ5xbWhIimUOn60Csx8tscpgvWM4lM8jmzz9SItJLyQ5E8k+secUDnNkoRCo4/rZrsP7BmUfOD+042fPc0pIVU8c/dKizasZNz1SU+NlRtivb7zemTUmIVLps5ffae6a90dr39tn+N1dMH7h24+mjF55aNPlHs4vDrny4UhqFX5Q38W4XoFKQG5EPPbCy6lDn6ulFz1aUPH88drz3av3qWe92XHr9zMVf3jV16eRxYVc+bHnrilG+5PbMRp/WPLlw8ro5xT/+pKtkbIEydnzlVM+hrsEPVs8sGpUfduVRWSiMj0TBEhKJgiUkEgVLSCQKlpBIFCwhkShYkkYkHTE0ftzYBbfMnjNjmu9SmEeFKTtmOMr2wNnMl5XIffv2FRUVVVZWKv/7Ksgew7aobO6CubNcM9kecFue4ssPQ0cioctKZF1d3eDgoACU7lGVtKggNzfqgnlQjERCl5VIBcfGxkYBKJFIlBRxxpFiUMohss2IGzcdM2a0vfad6xT521c/+rt3HjSD1Xh2nESgtkEkt0xUyOLPbGKxmAKl8iESiShQeinIicibS0vK5syYWFw8PDJ8sX+gveP/7rljaeJHRyKrHALD2fh7NtyGzaBxx3Wu6ERyywz7aqCCbiONyc2jv/vzjRHyHyuns797I5L1CRK9SSv/ha1Tt1ldOB0/zaqtTGwmASjAceS0SMnM+eW/Oz90Y2TkN239I2Tk0bKJo/Pz7o4UziwarSXxTKR9RMnbqAc97yGbOaHfZjYnWFEgFOBc+96/uW135/WPugaVz3eUjMvLI5/3XFU+PzC96PnbpmhJvPfatHt1C9kVH1myrhTtjtHmaAVpLt9jBu7bnCtGr20vExW+Arwf+dD9KwZv5P39p13lE8a8umyqsuWZ/461XLz6nz/4q4ljCrQkzMtwWIsG9/VSqswb5hxXii2Y25qGmmtzy0SFrUCe2ejyQKR8YW+c6Qr2ufYrp3pce23Zwklz5itYIruGrrvObGSKfpsIKnOVptgfh7s/KJRVGI2GgiUkEgVLSCQKlpBIFCwhkShYQiJRsATMZ6NKKKgW157JFgX4FNGjz8a2yAsSmdMKkEgnsTHk9NtBxZflQWWNGCLtgT9GuOTGjRvdC0qVSOYd2Uhk7ooh0hIcSUfvrl271r0g+UTy38BO+KvIJFt7BkPPMkUMkTSCy5YtO3bsmPfoXVGfjXOv7bwgTbK32NtWZCEYE5QxcnQ16F+9R+8K+2wSMxujOXMlskFopY/4iizopckABej88uazsa8GEASRJLE75Q8AuQQt/lxbwVHpspWOOxXnlzefjTuR3AVp/K89Q2pq2rdutQ1IUQAVrqvBlUiHdVP4q8gkayNNNyxObGArw302aKvJOmWczwZXkclyZaDPBleRyWqhzwYFSxiNhoIlJBIFS0gkCpaQSBQsIZEoWAJHZP/A4OfNZy5dHjS2UNFrqOwXOCIb/3ii79IAvaX81tnRsx36Z1c6czn4PHpge2PpTx9fWRp2RVI9itMyiRR+96SuoydPT5k0obn1T5YqeW4jgyKy+8ivX++u3L6uXHbBEoVE8iT8ft7h4eGmE83d3/bRzaGhkIlUeHyHrId+rZFInoTfYd769bnTbV8TtoM2FC6R6klaDLt9jFcTieRJDEpl7Dh35rQL3d/67LV9rlXD8drwTTmULC2kcsr2tuTlKZ9GRpZsioOq9+qbyN69zVNXaVTEk42MRFatihw+bUNFzXE4ppWzZOOLWilmIS3Mdm5ipiaRyiceX9mrErkq0tjYotZtycbE35AlGV0LDeJNi0/vVQtXSyYHXtrXwqS07lr9fnpx/GioL/a9ROn62HbtdLABEEmE1sL54PDvq+5bPqqggPiY2fhdq6atZkvd2l2WF+bzTDkMk3QTqZ732KoEXeo3okGpXbcLFZuoZCT+RfuJrGKJ7D5y4MzCddoWp0Lo7ZzEbE0SO22uMErSSaEbTvtgWMui/wlFVRbjHJsJebs2izTScffCrQ/7F8k5WDBt5MGPPjVQ83r3R2itGlsr6WKBSJw6o4m0dozG2WcvtiWZQ3eq5PlVY4yYba2FGOarNTGnTAsWWqV7422eIbatorM4fbbXM/ErMdnj7aWXUx+WSN7BghlH0kR6ld+1aigHBPFkyjEuW6KN5BCpn2ifRNLtJt0gcS9SCS+xZyJdx5bJiCTcesZ/Xk/eiVPG/5OLgiBSeK5dd/gPlcvvKCocZ2xp7f9uS9M3d04p7Bq6NnR9+MXvl753bqDpwpUNc4urF0xK8OdnrRqKPda1k5RI+lKxvba1/zKy8Hpto/OiQNF+jpi9doTOFrE0h1QKehfRI0dKVvLbJDqZkucAWUcPBZMRya+nXnx3hMRKzb7DthcukcTYtcPBwrkf+dkXLcqcpmzOTJPRzsuvtvQqRI7JJ02xK5HCUSsihQc7BhZNHPva8pvjifytVUPZc0zXTlIiLXNtl5kNNURLTAiYmU03cz0aY9o8YMkS0kzMNjJS0dLSYi/clpjeBduZWtok+6zIO5GlDrtm+aSPlzBzlCRE8g42mJmNgHr7LjUdP7XiztsmTSjWtyg4/rZrsP7BmUfOD+042fPc0pIVU8c/dKizasZNz1SUpLVyKd6P9HpbJhNuw0uT28GCIFJRR9f55q/Ozr9l1oyppeMLxyldtlKv2numvdHa9/bZ/jdXTB+4duPpoxeeWjT5R7OLAZ0/fvIjkcd503OJO8lsZQKRigYGh6JnO3ou9q++966qQ52rpxc9W1Hy/PHY8d6r9atnvdtx6fUzF39519Slk8elvq9gRfVitltxbnmQSAKKSEP6tObJhZPXzSn+8SddJWMLlLHjK6d6DnUNfrB6ZtGo/LAriApQEIlE5bKQSBQsIZEoWEIiUbCERKJgCRyR6LPJcYEjMgWfjWzl0PIRgAJ+QTzX1pWyz0a2JBGZCfe+s5TIsH02wSkFItGm41Mg4iMl+WyCkziRaNPxKxAx5JJ8NmX0Fur1zlrIWf2G/WvUYDR1K4mnSuTSI3jryRr2xdBMYK+Lg4fKbrXqoE3Ht00ny3w21BaLmUZnRcfJNNbowOmhk5QbTM/IIdLBwUNnZ4Q2Hd82HRBtpByfjXULd8UR7mfLAg6Jr/NsRPIdPM7rN6FNx79NB8Q4Uo7PhkOksbySPCI5L+J3WZEEbTq+bTog5tryfDZsrx2nxxORceMN7cLh99o2B08SItGm48+mA+J+pCSfDXGb2SRtIyuqa2vZFW+4MxsHB4/Tqk1o0yE+bTogntmE7bMJciEwtOn4FAgiScg+m2CXpkObji9BIZKE6bMBtlhibtt0ABFpCH02uSyIRKJyWUgkCpaQSBQsIZEoWEIiUbAEjkj02eS4wBEp6LPhx0AkVdYbaHQBCshNKhDPtXWl5LMJj8hMuDedq0SG6bMJi0i00cgWiPhICT6bkIhEG410gYghT91no2o5Y2hwtMJo2ykHA8dAQwhvQREO9Gijkb/aTWb7bAgdFxkP1vVghWEsOGYoLsejQxXGa07RRiN/tRsQbaQEn43x1ZMVhrbgcO0Kdo8OD0i00QSw2g2IcaQEnw1NTnIrjBci4+s26J/2kM28eDW00chf7QbEXDtlnw29fJKjFYbjpHHptZlFRaIVpLl8j33ehDYa+avdgLgfKeizoScwOyu27S93Wmsu3ijanTRJZjbUfpp3cifyaKMhsle7AfHMJnifTUpR4m53ltBGI1sgiCSB+2xSIDJZVrTRyBUUIkmwPhtBIumXskhTbttokgoQkYbQZ5PLgkgkKpeFRKJgyTeRDzzwwIkTJ8KuNipk3X777R9//HEQJfsmcuLEifn5+X19fWGfE1TIGh4eDqJY30QqOCr/X7x4MewTggpNkyapj81gERlQbQj6bDJBgTIAjkg/0WgeY3Tph9dZ6qqxxXQFKqkMWB9bASLSp88mHURmzI1pJFIukUI+m+CJDMFDkxl/AplBpD3wxwiX3Lhxo3uZQj6bwIkMw0ODRMoj0hIcSUfvrl271r1MIZ+NRuTOim3btAgy89kzd5EbO5GOsWcJOXpoKLOIPfbMgz2F3gMTpkUVGKl0dOTQ0PID1T3nsmDh2VXz0ksvKf9/8vq/iLtqLGFvlAdIGpE0gsuWLTt27Jj36F1hn02t1aHFNdnYiXRa+YaWs4fGvoX1uLjaUygeHfw0iXQusb0uRPrJxRysD1dN3sINCnLDX+4Xd9UQmkjmV5njSANK/av36F0Rn40tzls1IbRzTTbtViIdXTVUTjcPjYurxpM9hd6Jo5/Gyf/gTqSvXIR7PMldNdu3v6QSOfxl6q4ae/Su5JlN6s4vz3IgkjO2bPBAZNxVQ8nFQ+PiqvFMZFI/DSgiWVdNfuQHRCVyOHVXTeBE6lAqXbbScXv3Noj6bGxElnFNNh56bQ7HVg+NzbnCddV4ubRJ/DQeeu34kM3M6KnX5uYScdVE7vup1kYOp+6qsZl0AyBSQEI+Gy6RXJON2MyGnWtbnSvEbWbjhUgHn4reM8ZnNlxHDtV7mhmtTaD3XCKump+u36C3kam7aoASGfZ6Ng4C9U4fsRelBPN6lZx4ihjqejaOCvX2oLAjRyCXP+UEkSTM9WygSsSRI5rLj3KFSEPoswGunCMSBVxIJAqWkEgULCGRKFhCIlGwhESiYAmJlK6go33jjzIf272z5THzReYyqi3DJyS4ioApJDKJ3mrvj/b/JY+QnyyY1PjN4NcD331vdMH6OcVzi8d88e3VP8au9Fy9MXlswT/Om1A8Wr+XGSyRZnAb+2p972JeXc288R+JTG9txPTKqZ6Ba8MFeUT5dz1xOJPGFNz6vdGf91w1kt0/rej+6Xo4UrBECl1xJkaTLoEKZ0Ii014bAV26duPfT/WqR0LIbZPHjcnPO9ZzRf9pwuj826cURvv+cv7KdeXrvVPHV824Sfslk4ikfkAi014bAUX7v3urXX3lyyOzi5dNKVQ+vPw/PUPXh0fn5z23tKSwIL8pNlTXeVnZvn7O9/66RH8mHoBHJ9FBl//C+K36w9byl6n+O76gjrF4RCJo7sORR943MiWi3hPM0KR6iabzsp0eCYgIiXTTx98Mfvxn1QLx9OKSyLgChUWFSOXrjKLRT5Srsb3vnxs42q22mk8tmjytcJSWKQCPDjVkNAtiR5TmgjocG4W1jXQdRzr5hNy289b+ERQS6SZ9WqO0iNtvL1U67q8vX9v9lfpOortKCx+epcat/Tp6sXPwWkFe3ou3TxmlRcQE7dFxIpJKpiNHt1SOvTZnPSinOhBv27HXDlT6tMZoET+LDX3QeVmZa//DvAkzxo9yyBSsR8cDkUZepeHUuXQmklmrx7UO7d62I5HByZjWLCstfERrEd87N3Cs+8pP5k+81NlxvCWq1/OxTQ+z+QLw6HjotU0i22pq2rduZfpvP22ko0/IbTtv7R9BIZGOUvprpddWPigdtNJNKx92fXWx4/K1f70j8taBD7+7dl1PphC5e+/7xEQzAI+OLyLpIugClJ0nxgrUCMLX6jsO253W/hESEukoZU6jzGyUD1vKJ80uGq0cyY6T3VdvjPzbHREdQV22NhKVkpBIESGRwQmJFNEet14blZKQSBGd/LLNeWaDSklIJAqWkEgULCGRKFhCIlGwhESiYAmJRMESEild2bEWTmh1QyKTCJrPRlS2+KAk69QjkcHXRkzQfDaiQiJVZTyRAH02okIiVWU8kdB8NvEYNEtQm3UTa5vVqvNV09oP3YiUtU6PBCGRbgLnsyE8D82WurW7WK8rtbdE0RS2pqoTsbqy1umRICTSTfB8NnYPTSId03DavAouvXaDvHV6ZAiJdBNAn01ie8JDQygfAQWJnnMP2UxtcCZSZt1SFRLpKIg+G2Lz0BDWfb2twhxcbo5WkObyPbZ3BegyN0hcp0eCkEhHgfXZsB4as9zl1dWklrxAz6RMglzn2vLW6UldSKSjMt1nE1grFqyQSBFlAJFBTTwCFxIpIuA+G71zDapbDVhIpIjQZxOckEgULCGRKFhCIlGwhESiYAkikSgUFCLvv//+xsbGsM8GKmTNnz8/Go0GUbJvIoNW4x9P9F0aoLeU3zo7erZD//zDB++LbxVdmMNZkC013hU9sL2xVP4i7+mTTCL37dtXVFRUWVmp/C+Q/ejJ01MmTWhu/ZOlSiaFtMIgUl1Lvbty+7pyWbsMQEgkpbq6usHBQQEolRFJ04nm7m/76ObQEJ9I30r6zC4ZkQqP75D10K81EklJwVEZYgpA2fr1udNtXxO2gzYEhEjlUp9eDLt9jFcTiaQkBqUydpw7c9qF7m999tpG7KBpmjFCt+noLfuCMWVeg7sMWVpI5cLvbdEi0kdGlmyKg6r36pvI3r3NU1dpVMSTjYxEVq2KHD5tQ0XNcTimlbNk44taKWYhLcx2bmKmJpHKJx5f2asSuSrS2Nii1m3JxsTfkCUZXQsN4k2LT+9VC1dLJgde2tfCpLTuWv1+enH8aKgv9r1E6frYds05WPkzm1gspk/GI5GIAqWXLB8c/n3VfctHFRQQfzMbM5rVaprhml3oYEO78SUJkXQTqZ732KoEXeo3okGpXbcLFZuoZCT+RfuJrGKJ7D5y4MzCddoWp0Lo7ZzEbE0SO22uMErSSaEbTvtgWMui/wlFVRbjHJsJebs2izTScffCrQ/7F8keLIg28uBHnxqo9Q8Mft585tLlQeNXT20kLyDcZcEYwje+OBNJN5HWjtE4++zFtiRz6E6VPL9qjBGzrbUQw3y1JuaUacFCq3RvvM0zxLZVdBanz/Z6Jn4lJnu8vfRy6sMSaTlYEONImkivSkIkTZ1twZg2rvHFcxvJIVI/0T6JpNtNukHiElnCS+yZSNexZTIiCbee8Z/Xk3filPH/5KJhEik81647/IfK5XcUFY4ztrT2f7el6Zs7pxR2DV0buj784vdL3zs30HThyoa5xdULJiUnkrguGNPANb4kJZK+VGyvbe2/jCy8XtvovChQtJ8jZq8dobNFLM0hlYLeRfTIkZKV/DaJTqbkOUDW0UPBZETy66kX3x0hsVKz77DthUskMXbNOVgQ9yM/+6JFmdOUzZlpMtp5+dWWXoXIMfmkKXYlUjhqRaTwYMfAooljX1t+c3IiOWYXc8EY3atvM774mmu7zGyoIVpiQsDMbLqZ69EY0+YBS5aQZmK2kZGKlpYWe+G2xPQu2M7U0ibZZ0XeiSx12DXLJ3285l48EGk5WBDPbHr7LjUdP7XiztsmTSjWtyg4/rZrsP7BmUfOD+042fPc0pIVU8c/dKizasZNz1SUhFPLFO9Her0tkwm34aWJc7AgiFTU0XW++auz82+ZNWNq6fjCcUqXrdSr9p5pb7T2vX22/80V0weu3Xj66IWnFk3+0exiQOcvSfIjkcd503OJO8lsASZS0cDgUPRsR8/F/tX33lV1qHP19KJnK0qePx473nu1fvWsdzsuvX7m4i/vmrp08rjU95UmUb2Y7VacWx4kEpb0ac2TCyevm1P840+6SsYWKGPHV071HOoa/GD1zKJRGA6XzYJIJCqXhUSiYAmJRMESEomCJSQSBUvgiPQaaYHKUoEjMjyfDTRlfOxt4ij8BTqDeK6tK50+m0y4DY1EpizYPhtK6JhJ61GERyRsn00KJykcIZEyFIbPhjgtIBN9oZ6sWVNrXTUBHTNpcswkotE0IulxEjNmsp7YLPDZcE0zOqTcN4aiYyZNjpnEcbkQyTmxINrI1Hw2iW9MK+nshUXHTLocMwmOnYnknVgQ48iUfDZ804yLOxsdM2lyzIRPZDg+G75pxp1IdMykwzFj67XjHb3DUQfQa4fks+GuFuP2Bgt0zJC0OGbK2ZNtjBYcjjqwmY2ARHw2qSy9gY4Z+ZJxsNJ77VTk12eT4kNEdMzIltjBck4sFCKJd5/N0X/WfK/pXQsGHTMBHaztxAIi0hD6bHJZEIlE5bKQSBQsIZEoWEIiUbCERKJgCRyR6LPJcYEj0ms0GipYhRYvDOK5ti5/PptMVibcOs8KIjPGZxOu0OXjKhDxkWn22YQrdPm4C0QMuYjPhuOkMV/0zF3VJvEc3ExmteVYP3vZhcedGkKXTxKXT8b6bKxOGsvbyDWm5tkj1lSAmg0rmOHLcSaS3QWV18dOaaHLJ4nLB0QbKeKzsQRI0g2fJq3FareuakMjRBcyz6WNpHbB5PW8U1ro8knm8gExjhTx2STFhU0ZX9Wm3U5kfLURQSK97JT5GV0+SVw+IObaIj4baxC5aYzVvmwhu9S+2Laqja3X1pGiAoCdzTr0LhpqauZt1ZpDDzt1uWzo8rG6fEDcjxTx2divt/ssxpxj8GY2VG43s465C8siOUl3yiCJLh8Xlw+IZzaZsZ6NLKHLx1UgiCQZsp6NLKHLx0VQiCRZuZ6NLOWSywcQkYbQZ5PLgkgkKpeFRKJgCYlEwRISiYIlJBIFS+CIRJ9NjgsckeI+myxf4SZXXpQP4rm2rlR9NikQmQl3k5FI/wLjs/H5bkk0vqT1KNJIJBifjT8i0fiS3qNII5EkrT4bWyCYxuHPRx553wg2c/bfUELjC6zlbTLWZ2NvB/mx3zwrjDWEFo0vgJa3AdFGCvlsGqx2Fi6RfCuMwwpgaHwBsLwNiHGkkM9GF2VnIQ5EOlphjMuGxhdAy9uAmGuLrWdjtbPMc+q1bVYYt8uGxpeQl7cBcT9SyGdjs7NQHMYnPXoP7WroJ2h8Aba8DYhnNiH7bND4Il/iBwuCSBK2zwaNL7IlvrwNFCJJZvlscsn4IiTx5W0AEWkIfTa5LIhEonJZSCQKlpBIFCwhkShYQiJRsASOSPTZ5LjAERnSejbxR42P7d7Z8lgWm3UI/MhfEM+1dYW4ng0byCZOZCbcBM8lIsH4bHwrecyaF6FfR4ZAxEeGvp6NFCLRryNFIGLIJfpsBNakocLMqz9sLX/Zx2I2tNCvI8evk5U+G/c1aTi2G3Orz4zsZUO/jgS/Dog2UrbPxnUFEJ7txonIpBlpeNCvI8WvA2IcKdtn42dNGstmnxkpoV9Hjl8HxFxbns/G15o0pu0mOZHo10mXXwfE/Ug5Phvfa9IQziDRkcgkC3AS9OtI8uuAeGaTJevZoF9HhkAQScL22cgS+nVSFxQiSWb5bGQJ/To2ASLSEPpsclkQiUTlspBIFCwhkShYQiJRsIREomAJiUTBkjQiD370qfG5cNzY8ltmz5kxLeyjSyYpkbpZvo5OumUlUvjZNE2krkVlcxfMneWaiXpUrIr7uDhIIZHwZCVSOH7HTqSTqMAzTgxiWqmU469ByZSVyFRiHJ1+omMf2VBIbnyNj7WRUhUSCU+ccaRwHLjTTwqCu/e+r3x4bNPDSYi0vUKc9bVQ3hdtO9Wg2hNruO2s2LatljAh32w4uPmDSAnqxrV11EFwvDjJothQjPgzGwGvjJ1Iumk0iGSTuBDJ9bWw3hcziJabmBoEmEM9S+Bt3ppmw9Xlo4Rm2tLAs+vw3teP8qQA20iFyH+qf0D58Oaa/zKIrGxQPVyNVbO1JFwiN5M9TVvbub4WwqZPZCfcxO0sH4li6W7aQKhBtARiBZhXCOsHQrkqwHGkCJE0IpwRXpsjkZzEDf6IFCuBODTrVlF+IOTSVQHOtX0TyfSHXF8L5Z8h9BduYh5PTK+tFUBML7b/EhpqauZtNceRvELsfiDsv10V4P1Ij0Sa9yMtLQhnRqBd1Irq2lrbxIaTmMsTO/dQZi37y50nIA4lmCm5by7gmHjsEzGUkwJ5ZqPLA5F+hc1M9ivY59pIJMqvMivSAonMfmUWkajsFxKJgiUkEgVLSCQKlpBIFCwhkShYQiJRsIREomDp/wHHppNHAp2OywAAAABJRU5ErkJggg==)

#### [3]图形化界面使用

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img018.35b9f044.png)

#### [4]命令行使用

执行已和插件目标绑定的生命周期：

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img019.6771554e.png)



# 第七节 profile 详解

## 1、profile 概述

### ①单词释义

![images](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAUsAAACFCAIAAABdQE25AAAYQElEQVR42u2db3AURd7HG/xbWOhxXMkdqUIWUtZhKVJUVCqJVLmSClCPh/IqLzYRE0oRKIIiXp1mI24if8QXIY+liMlqQUp4wRuiIqmLq8clETVehS3rXiC6eZDA8/Dwp7QsUPDP9cz09HT39Mz0zG6yk+H3eZHa7J+Znv71t/vX/3494bfffkMAAESUCaBwAIgwoHAAiDKgcACIMqBwAIgyoHAAiDKgcACIMqBwAIgyoHAAiDJFU3i2vax+N3ld0dzTMFyt/Vue7GlfNpV+av4LAEAwiqTwobaylV30v0THYPxwGSgcAApOcRVekTy4Y9mtks9B4QBQENwVnm0rq+9ivWgNVpb0C+l4b33LAPcp64ezWuXe10ikB9ejdq82/MyBxqUt/cxP5hY77wAg/Cgp3A72q9fPk32BaFL6QyL+IArnvXr2agAAuKCqcFHSopI5vZkapm8Kv5J46Vk3hZs/r0sPrsMt97kD66o1f4H8CwCAI2oKZ7XEiVP2Bca33/GXqbJf+VS4vdNO3gFfHQA8UO2HW1o1+8N6q+75BSR/04/CUXdjdapfljxw1AHAA1A4AESZwnjpnMLHwEsHAECNwoy08Qof7ZE2mC0HAFUKM1smKNzhh8zAmD+Fo3MyR53rBQAAIEO1H96MUqbGpCteBIXrnzmseNHwqXANbsULeOwAoIT/kTYAAMYPoHAAiDKgcACIMqBwAIgyEOMFAKIMKBwAogwoHACiDCgcAKIMKBwAogwoHACiDCgcAKIMKBwAogwoHACiDCgcAKIMKBwAogwofPxx5syZPXv2bNiwodgJAYqAX+uDwgvGr7/+unfvXmyAp556qlDX/Omnn/r6+oaGhk6dOvXDDz9Mnjx5+vTpd911V3d399atW2+66aZiPzRACK31QeGF4ccff3zzzTcvX7587NixN954Q/h08+bNa9euvfnmm31d89NPP92/f39paek999wzc+bMSZMmXbx4MZfLff7559jqq1evnjsXosWHgjBbHxReAC5cuPDqq6/OmjXr4Ycffvrpp+02/vDDD7/99tsVK1aoX/O99947cuTIypUrsXXtn+JKfdq0addcc02xHx0Iu/XHXOFnDrQdqVwfod3mR48efeeddxYtWlRVVYVdtSeffNJuY/z+pk2bsP82ZcoUlWti677//vsbN270rPhxu3H99dcXOw+UAesrUFjrKyncCHVqxTbV4yiqhoUwIyiS74++jWlgVsVgrFrUR5QerMlx6XTltddey2azRtaVlJTU1NTcfvvtxkdPPPGE3caYQ4cO4b+LFy/2TA/ufTU1NWG/7rbbbjPewcXo0qVL991334QJE9hvYufwpZdewl++4YYbRi87jVPirMwkBlULhmkeKUl+PurWN8+0Uz7xilh/YYZLpyvjy/puCrcdEuqA+wmBWoHIxMcuNKpm41y9j0DLxMbmmYeZRd4Kx4bcuXOnkOP0I2rjV155BVfqxoiI+hBob2/vN9988/jjj9N31qxZg+09ceLE2tpa7J6xX961a9ecOXPuv//+UcxQo8bkg9Wr1u+awtHYHS+nFbZcg5/bMdbXIhohBYWPL+uPTRsuV7h22eGGNKo36hF6fZzpmZnJXIo2FEz0dS7cshCMGf/bkFvKfXOEraSMMspGZabf8a9waVUtfNTX13fy5Elcxxv/Pvfcc5s3b/bMsO3bty9ZsuTOO+9kr4mL1Mcff4y7Z/F4vLq6+tprrzU++uKLLz755BNc5Xsbwi+SE50leBQDZ4UHsD69l9z622MtG7vYVGX9Wd+HwseR9X31w4289nnip7vCU/3Ue8dZH9PzVzcMvYvud5Uy5wqT19rPU6hZNzl+P4WajFtYbbh2nePcKStmMeKUHKwNV7Gx0QFLpVLGv88//zx2qzwz7JlnnnnhhRcmT55sv+aFCxdwr+/s2bOPPvqoMQZz/vx5XCa2bNmibpCgBLK+u8K9ra/dNEeEzb52sD7Thvu3foEVHhLreymcO4fAjkJnzF3hvXF65gHNaybTbT+3/sX26IwxtXjnzB7T3lIvnbXfGCkc8+KLL2KD+bLxqlWrXn/9ddYJFK75wQcf4Bp927ZtSO+2bdy4sb293fOyATjneCakiecR7u4K97K+8B3mag7Wd/TSVaxfYIWjcFhfTeHc+WFG/WoMaeSt8OEGWkSoOUdYhTuYqvYEb3vLtKLC2WI6xm34xYsX29rasHuGX1+5cqWlpYXW6C6sW7eutbWVHUel18QWPXjwIHb/li9fXlFRgfR6HRt769atnpfNm1Fow72snxVKgmnlkm4H69sU7sf6BVZ4SKzvX+Hk9VgpXNYbxKaKHxZqAVqpWwo3rZswa/2xbsOxPXBljLtV+PWxY8c++ugj/KmnjbHNHnrooTvuuIO9Ju6JDQ4O7t+/f86cOY888sgtt9xifDQ0NHT48GFcLDwv65fCjLOOjsJRu4P1kaVw/9YvsMJDYv1CjKW7nwGap5cuFhFiy8ojnm240JgXWOEqo6kvv/wyzv0bb7wRv967d++MGTOMqtednp6eU6dOPfbYY/SdNWvWxGKxy5cv19TUzJo1i/1yR0fH7NmzH3jgAQU75QdX1yszOl66QhsewPoFHksPifXVR9roTKNC74vFa6SNzpTSr/E1t5s9zHEXcTSOKtz8oTCpm6/CcU/p6NGjRtZNmTIlkUjQwU9pBY/9q02bNiWTyUmTJnlm2KVLl/A3n3322VtvJVmGq2p8hXvvvVcoVSdPnsR+IPb9VC7rF+9OuE5eY+ne1ncaaXOwPq9wn9ZXVfj4sr6iwpk8QrrbrC5yjzY8lkBdXXrFwc6X2Hwwcw6szvN9pvJmhgkTHT2xtF4OzJUtGkFny1i++uqrzs7OBx98sKqqysnG77777s8//4z9K/Vr4rp54sSJLt/B3bzt27dXV1cvWLBAPbUB4fpKfg5+9WjD/Vmfr01k1mf74b6tr6pwwVIht763wiVLxGxnA7uh7KWHgCAKR+bKZGwV7EdJ1y1u2bKlsbExQEv7yy+/SJcfnz59eteuXfPmzVu2bNnoZgnVCTf+glQXjSl76SEgiMJR6K3vpnCP5Z9mve7hp10FCkf6EkKc6diP+vLLL52GYfzy/fffY+9u/vz5ZWVluABdd911V65c+frrrz/77DPsuS1fvryysnL08sIahZH4a8pdtqtA4Sjc1h/9nSfCunSGcNnYOZ2KFHyHMDbNhg0bcAdseHh4ZGTE6IaVlJTcfffdFRUV7KKI8CKsS2cIl/Wd06lIaK0Pu0dDzVtvvYU7WnPmzMGvcV+OLlcErgYKYn1QOABEGVA4AEQZUDgARBlQOABEGVA4AEQZUDgARBlQOABEGVA4AEQZUDgARBlQOABEGVA4AEQZUDgARBlQOABEGVA4AESZYipciLapwUTGd0TlOwpwEfPpO8gjOhUfrz/IQ1uxE1xhNipz50AgWwQ1Nv6RGTTe+qH0gCdZjE32EAIat9QWKp+NxDjU1nii1vdeemy+VtTMHkdTnkyWtrQw6SHBwg7HRVuQIE21OZ8HV3EG4EudLdMcUf+m53X0B0+kO1A9H0dYjE1QiKJeRIXby58eYqU0gXYjNuS1UsjXIDFA2cytSHbEMyv5Am2LXqJLK5ao6+ryqgh8gFOSjkkTL3/w8mR6UaaTHPfBHgzAHRKgYWrJdmX9kJBFsZZe875EOfGMdoWG3FJT4cLP2Qs6J9sDRuGZhYPrpzteU1LhmgovOYP6WoOJnJQ6LRq3tFDpBQnZ69DpYn3nN1YErZctGduel69BmEB0zG39ti7FUbit7Goh32L7iEWda1lbIQ56f3KdU7ShIIavPUHPyhEx5G2dBlFaGJFnjYI+z/FTy+pmgUDmgT7Wp0KFZT+zTcMsHIbG6nPV+GpNKIVf479ShTu04ShgeBbWf0loFaXZhmvJ2BfbsTDD1xrsYVU6/g8eFBFqvSGZp2AemdQwXI2zF//NLNQDOQ6YGRjouEWJx+qmcFxEM3H2FkHbc1eFnz608+2zv4sN/09O+++2JU01+TnGHFzmiprJ8mHxO49X9A/wha0umTzeQt1dvUJVjbKmiwrbbE+sff1cw9WcsYcmBltiz4wdfD7qyUNJvvBJ3lGL8Satm3nsoWOZto4c2YXTTMsHm5msgK3kWSXGOk/XU+GyNrxhUabeLKnZ9sZcDTk8UDXCmVmsuTYcpxanHydJojeX7LKOLlS8u+AXOFWvosJRogvF6QnEXEOqrHYfCvc4SkxPgfG8Cnf3VPggKlu5quqP2X2tH5wvW7F68TRUEIzquRm1EpfJ3sNhQ+R3zkzHe+tNr4z7re4+BavXjZZcu46sY8xW2PKDVvVWHfkILWzd1xAb10+hBjaLAnIo2Ylkcy6jlz/r/E1R4Rk00M/X9+ZNjdJTp6Zw1364pMiqPLzhWeh975yp8Aa939GMUuYFzda+3FarDuQxCDJEAoH3zOx0CQWf6CCuHNOGUznZFK6MGH8ePxpfC5MCsKCvsTUXQ7mY5byIjYe73yfgqfDjs1esrfoTfryubQN/KJjCSe1V2WcVcZtbYkIVzugQl2yrdlB/WprbHmeMs6fnGKNxgnfHR9736bFzCmcrF6Me8VSOdewmrcLtbTjjzAtPpDlEpQ3eCqfVlsyVDRpHMdu2rjOH4pojQIu7PrJQz5R+Up/au+XHKyoG+vt9BWznsr0z1hxrGbacNdceGdMPr0t07WbPCDDcCn/J8OOlew/Hqo8CeHvp5X9L6E1ZIRVuO08DV4q4eJkK57tbDmOYukpLdffJd3+YOxeFz0q9erb39xwVHgDFNlxB4fRX2GO39cOnsk4pSX9lX/dIJerUrm8onPbGRYU7uTbm0LdR8fkdTh9qazsRy/UyI21MMRUlxwiAPIh2nkGu4WCsM0CP9MyBtiOV663umFxFtJKVtOH54bMfTouK5sexLiQzHqREcRROn/qArQ1Htuko5sh4yxC0DdcOIfXtK7IKZ8dyWFeWF7AwdFw4hbu24fYuqNZuVB5hlGA0sGwPlq+MsrYha3J9qnD8Q0M5YhtuG9c07oW/TC7le+Az296GamKdzFh6/LDRk0fkYdn5C6bHbnlSJNttX1bEYWgN8cqxK1wrZlyb5NuPUFU49tKtzlGCHNJmd3CUKb7C9SKO8wsXLLG6Mr/DihD/q4+QMbWDr24Jva+vNlw0zxi14YKErFMZJW24g8KtBMsUrrXGKNmzEKVWShVuGwiQnh6lnu3dI8sW5MTZsn2xHfoESqoXad0HXuFpZmCPz/YACWAVzo3PCQ2jVOGMfxFkToctQuR2HajepQ1nAvhrIxSpfvwigEPhX+HsmwHhdGX2KIR+OM1E7X1EZ1Yo+lg6VbjPpQh+23BbYSq+wuOxVIsmv3LiLXsonGkuBB8hV17Rj+I9Vpts9cNNJx9pCcbFkZ/7CHY4jG3FizZwmBuOdR03xUx9BPuUe/6zZTSjzpw7dyvq04fucAJaeL/XvqaoMAo3r8kNNGA3ihvAMyvWcuq2tKBmsxIn3paPHCjuqlWJl06STm3Ju1VMV03/7SLDezdcJvVi568Nt69+kxW10ZgtU/DSDWTz4ZwjJDQgdKy+PJluQpmlLbnmHr74mlMVrGmol26UOX5thupsGVF4bA89PJQ9M9hwLsy79COVg259ztUJXrrs1GRhpMPNS1eeLZMs4HFdNUQnHcQhFfaO+c6WjTqswm1iqyNTZfxcBe3/yJw0YzTFn8K5NRVWRVvncEi9eSNJVap6d8dZAysNyl46Qd6GsxWE1W/Urx/XVhOUcoPDqCONVhrft8ZBhHLm6BsPtbWh9X4Uzqw5YWf1yUl4++K9/63bxWNUmTaGqnd3XuLiPjXAjwJydtEGF9YFciukCjedc7rEg64RSnPtHxlr9Lx7pHaemAswrsa7FxU6OHL13f0MGUcozqMr3D1SCgcAQAAUDgBRBhQOAFEGFA4AUQYUDgBRBhQOAFEGFA4AUQYUDgBRBhQOAFGmSAp3iFPDbCyTLFSSrA/PC9nuSMlWB9/pBIDwEI5Yq+JqYcleAlvQCA2/e2U57HqW7wTwl04ACBWhU7g0+Jk94E7+7bkQ75XE6FZWeNAgbQAwpoRL4dKNXN7x0snOJz+blqWxBJQVrnJ2AgCEgXApnP04S6L8lJFYtmzoD3ZPrGPcfzdssanN8OncBlUf6QRHHQgnxVG4tFOtk6BHDlitsXg+iY0AB57QEGVWlEIzqIgWllCPN2yczaCeTgAIH+Frw3U9S4PFmnpj4zTqcYh8jrfRjdyGs90zs9MKTiwE92SOQ1FPJwCEh+Iq3IyvYiqHC7Wh725vQPW0E66PnI/wgY3yOSQQiUH2HcfSvdNZtBgAAOBKERXOBDOy929tAaisExu1kKBY5In0QRKXNx8XmetFyxXuL50AECqKp3DB4+XDLbKRq42ecIJEpTKiqdfm9HY1ti+vA1/FIK1ShaulEwDCSbEUzq8n46NYSyLdc+HpjH8zcTG+qt/ZMiFgqFThPtMJACGjaKcLi40nCaBr6Jb7phXrkxlUZ1ezZZkzMZSinWrXyUkWq9gUrp5OAAgnkdp5chVHOwUAOZFSOAAAAqBwAIgyoHAAiDKgcACIMqBwAIgyoHAAiDKgcACIMqBwAIgyoHAAiDJh2FuW5TeEMtSlBxdmPMI/6Ej3aZvbVOQ7Q6wVqfIIEwH2pZInKrHiRiSSzbkWJoaEtth2QZ8sKA1ZAB9wL41nkAzuifQ9s6XsonqVJf36r1ABY90CY4FfhQ/t29I3dcXaqj/lf2tW4XxUYyesgE1MVAYHjI1fibquLmlANUk8JmaXKPfa9xOVkLiRI9ZFuLBwiNtebv0WZ0JzJZo6EkDksvBSDrltD7CTSHeger6CsAex1Rf/JxKoKwcxbcYVxVE4W8gqmpOxVMZF4d6RGG17vJh9nfb2Srtk27pcLdnKQragaD8ZbiBf8x/7jUlkRaIOde0mbbi2mx3fS4sMZSkcSaK7KVdzcrMYCkfOFZOpcLOuKbHC11rug1PAOSaybZCgOkARcVN4dl/rwNSVq6r+iF//399fffvcXfPP//Nf3+H/fje/AM240IY3o9bqlgH2C8SrtNxpaRsuNl92Z1J8h60yjMZK3OnN38hv/FauDTfCVGCFS6oMI8KM/GJ6v8NnHDiH8xuMB02SzbYp1NSQa9UqONTduGfGjtoTVqRq2dYd+z5Z2Dk7nnBtw4e6tg38YcXqxdPQ//79tY5z5U0180bPS9cUbjneTCvqQ+F6d1R6TIItvDlt2xFbXqn4A5y1QMPI4b53hiocO8A4eQszXAQ4EqxGiGBTkW/0dXmMGhmkMjKi5dgwI1saTxRgjAMID64KP31o59vHZ2M9I/zibPnfEnML3A/XipfppfMKF4IiKnjp7C5xzse2+sCMx64uBlXOHWhPZXbHGrg+ra2La1Qlkm45qhjo73cYMlRNAdvRcMt21+4AyZnKPjavDvPRrEnWgcc+DnDvh5Om+4H/xy565V9r5hWqH863Y7I23CmgkjRyg4Cjwq37t63LINTVNWC0V7E99qbMb4RmLfgEQqlcnOviMilnRcIkyRx1w2nAmdCQWxpcM+5VoemVUIXbpjBomA37YU9yhQPjAI+RNr37XbkEvffvPzdpAh+tsXT8ojanO97xw6SY0o6oU0+VgxWkUATtCreCQAkpMfFfiM91t/UtiOeWWmPpzSiFe7l6hWUbGmB67Gb/nwpvNCal2KkHVuHMUwtB45m7i64BKHxc4TWWrjnqg9+hmUs0Fx2NisIZt5CbAONKkn16zHEUSiyRkjbcISVIemu1h+k+UPIX3A7zs2XmiH1jOteP4qLCsQNv3YVznsXoUd45We88G46796x/5LsNV4pXCYQVz9kyzVH/1+//S3fRzX+/08bSp/3DGmkPBNEVIgUIHbAr3DoGsC3V29VPGnPSuXUYDLPVBQ4KZ85XiGfyVjj7ROaKl4pkcywzjNBuZIqZVAFzpR2HfGbLPGBCvvtuwxXOYAZCTLHXtB2MdZoOszBFLG3HhNUa9nEpyZmkNjkZF2EqCFkbGOC0Q6a7QcaoaHfglHlekqYis9vv3dUv0KlJXK/EVxsuW00ACh9XFPXcsvJkelGmkw5KiUsv9XE4fo5XaLfJ2BI714WkkZjz8NIV47far8Pelz5FXTqN6o2KzGOOwBx1V747j2O+ObThkplCh/oFFD6ugJ0nHhQ3fitEjwXyBBQOAFEGFA4AUQYUDgBRBhQOAFEGFA4AUQYUDgBR5j+/cXOqeO/QVQAAAABJRU5ErkJggg==)

这里我们可以对接 profile 这个单词中『侧面』这个含义：项目的每一个运行环境，相当于是项目整体的一个侧面。

![images](data:image/png;base64,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)

### ②项目的不同运行环境

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img020.a8144d62.png)

通常情况下，我们至少有三种运行环境：

- 开发环境：供不同开发工程师开发的各个模块之间互相调用、访问；内部使用
- 测试环境：供测试工程师对项目的各个模块进行功能测试；内部使用
- 生产环境：供最终用户访问——所以这是正式的运行环境，对外提供服务

而我们这里的『环境』仍然只是一个笼统的说法，实际工作中一整套运行环境会包含很多种不同服务器：

- MySQL
- Redis
- ElasticSearch
- RabbitMQ
- FastDFS
- Nginx
- Tomcat
- ……

就拿其中的 MySQL 来说，不同环境下的访问参数肯定完全不同：

| 开发环境                                                     | 测试环境                                                     | 生产环境                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| dev.driver=com.mysql.jdbc.Driver dev.url=jdbc:mysql://124.71.36.17:3306/db-sys dev.username=root dev.password=atguigu | test.driver=com.mysql.jdbc.Driver test.url=jdbc:mysql://124.71.36.89:3306/db-sys test.username=dev-team test.password=atguigu | product.driver=com.mysql.jdbc.Driver product.url=jdbc:mysql://39.107.88.164:3306/prod-db-sys product.username=root product.password=atguigu |

可是代码只有一套。如果在 jdbc.properties 里面来回改，那就太麻烦了，而且很容易遗漏或写错，增加调试的难度和工作量。所以最好的办法就是把适用于各种不同环境的配置信息分别准备好，部署哪个环境就激活哪个配置。

在 Maven 中，使用 profile 机制来管理不同环境下的配置信息。但是解决同类问题的类似机制在其他框架中也有，而且从模块划分的角度来说，持久化层的信息放在构建工具中配置也违反了『高内聚，低耦合』的原则。

所以 Maven 的 profile 我们了解一下即可，不必深究。

### ③profile 声明和使用的基本逻辑

- 首先为每一个环境声明一个 profile
  - 环境 A：profile A
  - 环境 B：profile B
  - 环境 C：profile C
  - ……
- 然后激活某一个 profile

### ④默认 profile

其实即使我们在 pom.xml 中不配置 profile 标签，也已经用到 profile了。为什么呢？因为根标签 project 下所有标签相当于都是在设定默认的 profile。这样一来我们也就很容易理解下面这句话：project 标签下除了 modelVersion 和坐标标签之外，其它标签都可以配置到 profile 中。

## 2、profile 配置

### ①外部视角：配置文件

从外部视角来看，profile 可以在下面两种配置文件中配置：

- settings.xml：全局生效。其中我们最熟悉的就是配置 JDK 1.8。
- pom.xml：当前 POM 生效

### ②内部实现：具体标签

从内部视角来看，配置 profile 有如下语法要求：

#### [1] profiles/profile 标签

- 由于 profile 天然代表众多可选配置中的一个所以由复数形式的 profiles 标签统一管理。
- 由于 profile 标签覆盖了 pom.xml 中的默认配置，所以 profiles 标签通常是 pom.xml 中的最后一个标签。

#### [2]id 标签

每个 profile 都必须有一个 id 标签，指定该 profile 的唯一标识。这个 id 标签的值会在命令行调用 profile 时被用到。这个命令格式是：-D<profile id>。

#### [3]其它允许出现的标签

一个 profile 可以覆盖项目的最终名称、项目依赖、插件配置等各个方面以影响构建行为。

- build
  - defaultGoal
  - finalName
  - resources
  - testResources
  - plugins
- reporting
- modules
- dependencies
- dependencyManagement
- repositories
- pluginRepositories
- properties

## 3、激活 profile

### ①默认配置默认被激活

前面提到了，POM 中没有在 profile 标签里的就是默认的 profile，当然默认被激活。

### ②基于环境信息激活

环境信息包含：JDK 版本、操作系统参数、文件、属性等各个方面。一个 profile 一旦被激活，那么它定义的所有配置都会覆盖原来 POM 中对应层次的元素。大家可以参考下面的标签结构：

```xml
<profile>
	<id>dev</id>
    <activation>
        <!-- 配置是否默认激活 -->
    	<activeByDefault>false</activeByDefault>
        <jdk>1.5</jdk>
        <os>
        	<name>Windows XP</name>
            <family>Windows</family>
            <arch>x86</arch>
            <version>5.1.2600</version>
        </os>
        <property>
        	<name>mavenVersion</name>
            <value>2.0.5</value>
        </property>
        <file>
        	<exists>file2.properties</exists>
            <missing>file1.properties</missing>
        </file>
    </activation>
</profile>
```



这里有个问题是：多个激活条件之间是什么关系呢？

- Maven **3.2.2 之前**：遇到第一个满足的条件即可激活——**或**的关系。
- Maven **3.2.2 开始**：各条件均需满足——**且**的关系。

下面我们来看一个具体例子。假设有如下 profile 配置，在 JDK 版本为 1.6 时被激活：

```xml
<profiles>
	<profile>
    	<id>JDK1.6</id>
        <activation>
            <!-- 指定激活条件为：JDK 1.6 -->
        	<jdk>1.6</jdk>
        </activation>
        ……
    </profile>
</profiles>
```



这里需要指出的是：Maven 会自动检测当前环境安装的 JDK 版本，只要 JDK 版本是以 1.6 开头都算符合条件。下面几个例子都符合：

- 1.6.0_03
- 1.6.0_02
- ……

### ③命令行激活

#### [1]列出活动的 profile

```sh
# 列出所有激活的 profile，以及它们在哪里定义
mvn help:active-profiles
```



#### [2]指定某个具体 profile

```xml
mvn compile -P<profile id>
```



## 4、操作举例

### ①编写 Lambda 表达式代码

Lambda 表达式代码要求 JDK 版本必须是 1.8，我们可以以此来判断某个指定更低 JDK 版本的 profile 是否被激活生效。

```java
@Test
public void test() {
    new Thread(()->{
        System.out.println(Thread.currentThread().getName() + " is working");
    }).start();
}
```



以目前配置运行这个测试方法：

![images](data:image/png;base64,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)

### ②配置 profile

```xml
<profiles>
    <profile>
        <id>myJDKProfile</id>
        <!-- build 标签：意思是告诉 Maven，你的构建行为，我要开始定制了！ -->
        <build>
            <!-- plugins 标签：Maven 你给我听好了，你给我构建的时候要用到这些插件！ -->
            <plugins>
                <!-- plugin 标签：这是我要指定的一个具体的插件 -->
                <plugin>
                    <!-- 插件的坐标。此处引用的 maven-compiler-plugin 插件不是第三方的，是一个 Maven 自带的插件。 -->
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>

                    <!-- configuration 标签：配置 maven-compiler-plugin 插件 -->
                    <configuration>
                        <!-- 具体配置信息会因为插件不同、需求不同而有所差异 -->
                        <source>1.6</source>
                        <target>1.6</target>
                        <encoding>UTF-8</encoding>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </profile>
</profiles>
```



### ③执行构建命令

```sh
mvn clean test -PmyJDKProfile
```



![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img024.4eeaea9b.png)

## 5、资源属性过滤

### ①简介

Maven 为了能够通过 profile 实现各不同运行环境切换，提供了一种『资源属性过滤』的机制。通过属性替换实现不同环境使用不同的参数。

### ②操作演示

#### [1]配置 profile

```xml
<profiles>
    <profile>
        <id>devJDBCProfile</id>
        <properties>
            <dev.jdbc.user>root</dev.jdbc.user>
            <dev.jdbc.password>atguigu</dev.jdbc.password>
            <dev.jdbc.url>http://localhost:3306/db_good</dev.jdbc.url>
            <dev.jdbc.driver>com.mysql.jdbc.Driver</dev.jdbc.driver>
        </properties>
        <build>
            <resources>
                <resource>
                    <!-- 表示为这里指定的目录开启资源过滤功能 -->
                    <directory>src/main/resources</directory>

                    <!-- 将资源过滤功能打开 -->
                    <filtering>true</filtering>
                </resource>
            </resources>
        </build>
    </profile>
</profiles>
```



#### [2]创建待处理的资源文件

```properties
dev.user=${dev.jdbc.user}
dev.password=${dev.jdbc.password}
dev.url=${dev.jdbc.url}
dev.driver=${dev.jdbc.driver}
```



#### [3]执行处理资源命令

```sh
mvn clean resources:resources -PdevJDBCProfile
```



#### [4]找到处理得到的资源文件

![images](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img025.97fa02d4.png)

#### [5]延伸

我们时不时会在 resource 标签下看到 includes 和 excludes 标签。它们的作用是：

- includes：指定执行 resource 阶段时要包含到目标位置的资源
- excludes：指定执行 resource 阶段时要排除的资源

情看下面的例子：

```xml
<build>
    <resources>
        <resource>
            <!-- 表示为这里指定的目录开启资源过滤功能 -->
            <directory>src/main/resources</directory>

            <!-- 将资源过滤功能打开 -->
            <filtering>true</filtering>

            <includes>
                <include>*.properties</include>
            </includes>

            <excludes>
                <exclude>happy.properties</exclude>
            </excludes>
        </resource>
    </resources>
</build>
```



执行处理资源命令：

```sh
mvn clean resources:resources -PdevJDBCProfile
```



执行效果如下：

![images](data:image/png;base64,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)

当然我们这里只是以 properties 文件为例，并不是只能处理 properties 文件。

