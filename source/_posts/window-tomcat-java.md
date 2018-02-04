---
title: window 环境下配置tomcat环境
date: 2018-02-04 18:32:26
tags: [tomcat, java, window]
categories: [文档留存, 安装说明]
---

搭建java运行环境时，tomcat启动出来一闪而过的情况。由于不经常使用java环境，所在在此留下解决方案，以备下次查找
<!-- more -->
# tomcat

在[Apache Tomcat®](https://tomcat.apache.org/download-80.cgi)官网下载要使用的版本，解压到C盘根目录下（个人习惯放c盘）

# Java SE

在[oracle官网](http://www.oracle.com/technetwork/java/javase/downloads/index.html)下载需要的SDK。
[Java SE 7 Archive](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html)
按照步骤安装

# 环境变量

`控制面板\所有控制面板项\系统` 查找`高级系统设置 -> 高级 -> 环境变量 -> 系统变量` 增加如下设置

1. `JAVA_HOME`：`C:\Program Files\Java\jdk1.7.0_80`
2. `CATALINA_HOME`：`C:\apache-tomcat-8.0.33`
3. `CLASSPATH`：`.;%JAVA_HOME%;%JAVA_HOME%\jre\lib;`

再次运行，就不会有闪动的问题，支行`localhost:8080`一切正常。