---
title: "Maven与Gradle的安装与配置"
date: 2020-05-23T20:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Maven,Windows]
featuredImagePreview: ""
summary: 在Windows下配置Maven与Gradle使用的镜像以及本地仓库。
---

## Maven

### Install

从[官网](https://maven.apache.org/download.cgi)下载Binary压缩包, 解压到指定位置, 比如`D:\Maven`.

### Configure

-  环境变量(系统变量):

    | 变量       | 值                 |
    | ---------- | ------------------ |
    | MAVEN_HOME | D:\Maven           |
    | MAVEN_OPTS | -Xms256m -Xmx1024m |

- PATH: `%MAVEN_HOME%\bin`

`MAVEN_HOME`默认是`C:\Users\Username\.m2`, 所以Maven仓库配置文件默认路径是`C:\Users\Username\.m2\settings.xml`.

自定义`MAVEN_HOME`后, Maven仓库配置文件路径是`D:\Maven\conf\settings.xml`, 将以下内容写入`settings.xml`:

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.2.0 http://maven.apache.org/xsd/settings-1.2.0.xsd">
  <localRepository>E:\MavenRepos</localRepository>
 
  <mirrors>
    <mirror>
      <mirrorOf>*</mirrorOf>
      <id>AliYun-Public</id>
      <name>AliYun-Public</name>
      <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
  </mirrors>
 
  <profiles>
    <profile>
      <repositories>

        <repository>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
          <id>AliYun-Google</id>
          <name>AliYun-Google</name>
          <url>https://maven.aliyun.com/repository/google</url>
        </repository>

        <repository>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
          <id>AliYun-Spring</id>
          <name>AliYun-Spring</name>
          <url>https://maven.aliyun.com/repository/spring</url>
        </repository>

        <repository>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
          <id>AliYun-Grails-Core</id>
          <name>AliYun-Grails-Core</name>
          <url>https://maven.aliyun.com/repository/grails-core</url>
        </repository>

        <repository>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
          <id>AliYun-Apache-Snapshots</id>
          <name>AliYun-Apache-Snapshots</name>
          <url>https://maven.aliyun.com/repository/apache-snapshots</url>
        </repository>

      </repositories>

      <pluginRepositories>

        <pluginRepository>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
          <id>Aliyun-Gradle-Plugin</id>
          <name>Aliyun-Gradle-Plugin</name>
          <url>https://maven.aliyun.com/repository/gradle-plugin</url>
        </pluginRepository>

        <pluginRepository>
          <releases>
            <enabled>true</enabled>
          </releases>
          <snapshots>
            <enabled>true</enabled>
          </snapshots>
          <id>AliYun-Spring-Plugin</id>
          <name>AliYun-Spring-Plugin</name>
          <url>https://maven.aliyun.com/repository/spring-plugin</url>
        </pluginRepository>

      </pluginRepositories>
    </profile>
  </profiles>

</settings>
```

## Gradle

### Install

参考[官方文档](https://gradle.org/install/), 或直接从[Releases](https://gradle.org/releases/)页面下载需要的版本, 解压到指定位置, 比如`D:\Gradle`.

### Configure

-  环境变量(系统变量):

    | 变量             | 值             |
    | ---------------- | -------------- |
    | GRADLE_HOME      | D:\Gradle      |
    | GRADLE_USER_HOME | E:\GradleRepos |

- PATH: `%GRADLE_HOME%\bin`

`GRADLE_HOME`默认是`C:\Users\Username\.gradle`, 自定义`GRADLE_HOME`后, 在`GRADLE_HOME`下创建`init.gradle`文件来设定镜像仓库:

```groovy
allprojects {
   repositories {
       maven {
           url "https://maven.aliyun.com/repository/public"
        }
       maven {
           url "https://maven.aliyun.com/repository/google"
        }
       maven {
           url "https://maven.aliyun.com/repository/gradle-plugin"
        }
       maven {
           url "https://maven.aliyun.com/repository/spring"
        }
       maven {
           url "https://maven.aliyun.com/repository/spring-plugin"
        }
       maven {
           url "https://maven.aliyun.com/repository/grails-core"
        }
       maven {
           url "https://maven.aliyun.com/repository/apache-snapshots"
        }
    }
}
```

## IDEA

习惯使用自己安装的Maven和Gradle而不是IDEA中捆绑的, 这就需要在`All Settings`中进行设置了.

值得注意的是, 新版本的IDEA在未打开项目前, `All Settings`中`Build, Execution, Deployment`里的`Gradle`只有一个`Gradle user home`的选项.

新建一个Gradle项目, 打开项目后, 再进入设置中, 就会发现多出来一些设置, 这时可以设置当前项目的设置, 也可以设置新项目的设置.