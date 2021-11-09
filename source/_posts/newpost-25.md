---
title: centos7安装zookeeper
date: 2021-11-09 17:38:53
tags: 运维
---

### 一、下载并解压

[下载地址](https://zookeeper.apache.org/releases.html)

```shell
mkdir /usr/local/zookeeper
cd /usr/local/zookeeper
wget https://dlcdn.apache.org/zookeeper/zookeeper-3.7.0/apache-zookeeper-3.7.0-bin.tar.gz
tar -zxvf apache-zookeeper-3.7.0-bin.tar.gz
mv apache-zookeeper-3.7.0-bin apache-zookeeper-3.7.0
```

<!--more-->

### 二、修改配置文件

```shell
cd apache-zookeeper-3.7.0/conf
cp zoo_sample.cfg zoo.cfg
vi zoo.cfg
```

结尾加上`admin.serverPort=8090`并保存（端口默认8080，不改也行）

### 三、环境变量

```shell
vi /etc/profile
```

在末尾添加以下后保存，如果path有值了，就加冒号在末尾追加

```ini
export ZOOKEEPER_HOME=/usr/local/zookeeper/apache-zookeeper-3.7.0
export PATH=$PATH:$ZOOKEEPER_HOME/bin
```

追加示例：

```ini
export PATH=$PATH:$JAVA_HOME/bin:$ZOOKEEPER_HOME/bin
```

刷新配置文件

```shell
source /etc/profile
```

### 四、启动

```shell
zkServer.sh start
```

