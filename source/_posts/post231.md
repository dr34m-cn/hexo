---
title: 在ubuntu安装jdk并配置环境变量
date: 2017-09-21 17:11:43
tags: 运维
---

首先到java官网下载jdk:[http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

我使用的是ubuntu64位系统，所以下载的是jdk-8u144-linux-x64.tar.gz这个文件，下载完解压到/usr/lib/jvm目录下
```
	sudo tar zxvf jdk-8u144-linux-x64.tar.gz -C /usr/lib/jvm
```
(如果没有/usr/lib/jvm目录先使用
```
	cd /usr/lib
	sudo mkdir jvm
```
这两条命令创建)
<!--more-->
然后打开终端，输入
```
	gedit ~/.bashrc
```
在文本末尾加上
```
	export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_144
	export JRE_HOME=${JAVA_HOME}/jre
	export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
	export PATH=${JAVA_HOME}/bin:$PATH
```
然后保存关闭编辑器，输入
```
	source ~/.bashrc
```
使得环境变量配置生效

输入
```
	java -version
```
如果出现版本号说明安装配置成功

以上在联网的情况下可以通过
```
	sudo apt-get install openjdk-8-jdk
```
完成