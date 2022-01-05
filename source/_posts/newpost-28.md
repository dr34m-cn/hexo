---
title: hadoop学习笔记
date: 2022-01-05 10:52:51
tags: 运维
toc: true
---

本文基于B站视频教程[2022最新黑马程序员大数据Hadoop入门视频教程，最适合零基础自学的大数据Hadoop教程](https://www.bilibili.com/video/BV1CU4y1N7Sh)，p19-p50

# 开始前准备(机器与环境)

<!--more-->

## 机器

本机（windows）用来操作，加上三台机器（CentOS7 x64）用来搭建集群，分别取名node1、node2、node3，并配置hosts方便连接

本机（windows）打开**C:\Windows\System32\drivers\etc\hosts**文件，并追加以下内容，其中IP地址改为你自己的三台机器

```ini
###hadoop###
192.168.0.46 node1 node1.ifnxs.cn
192.168.0.49 node2 node2.ifnxs.cn
192.168.0.48 node3 node3.ifnxs.cn
###hadoop-end###
```

## 环境

### 三台机器共同操作（都要开启SSH）：

#### 1. 安装**vim**、**ntpdate**

```shell
yum -y install vim
yum -y install ntpdate
```

#### 2. 关闭防火墙（生产环境请不要这样）

```shell
systemctl stop firewalld.service
systemctl disable firewalld.service
```

#### 3. 配置本机名

```shell
vim /etc/hostname
```

分别改为【node1.ifnxs.cn】、【node2.ifnxs.cn】、【node3.ifnxs.cn】

![image-20220105110945912](newpost-28/image-20220105110945912.png)

#### 4. 修改hosts

```shell
vim /etc/hosts
```

追加以下内容，其中IP地址改为你自己的三台机器

```ini
192.168.0.46 node1 node1.ifnxs.cn
192.168.0.49 node2 node2.ifnxs.cn
192.168.0.48 node3 node3.ifnxs.cn
```

![image-20220105111339690](newpost-28/image-20220105111339690.png)

#### 5. 同步时间

```shell
ntpdate ntp5.aliyun.com
```

#### 6. 创建统一工作目录

```shell
mkdir -p /export/data # 数据
mkdir -p /export/server # 服务
mkdir -p /export/software # 软件
```

#### 7. node1->node1/node2/node3免密登录

```shell
ssh-keygen # 四个回车
ssh-copy-id node1 # yes 密码
ssh-copy-id node2 # yes 密码
ssh-copy-id node3 # yes 密码
```

#### 8. 安装JDK1.8

##### 8.1 下载jdk-8u311-linux-x64

官方下载链接(需要注册，可能网速慢)：

[https://www.oracle.com/java/technologies/downloads/](https://www.oracle.com/java/technologies/downloads/)

![image-20220105115350394](newpost-28/image-20220105115350394.png)

百度网盘：

链接：[https://pan.baidu.com/s/176N837BQXyUoIt7HvF0c0A](https://pan.baidu.com/s/176N837BQXyUoIt7HvF0c0A)
提取码：0ld6

##### 8.2 安装

上传安装包到**/export/server/**，然后解压

```shell
cd /export/server/
tar -zxvf jdk-8u311-linux-x64.tar.gz
```

编辑环境变量

```shell
vim /etc/profile
```

结尾追加

```properties
export JAVA_HOME=/export/server/jdk1.8.0_311
export PATH=$PATH:$JAVA_HOME/bin
export CALSSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

拷贝到另外两台机器

```shell
scp -r /export/server/jdk1.8.0_311/ root@node2:/export/server/
scp /etc/profile root@node2:/etc/
scp -r /export/server/jdk1.8.0_311/ root@node3:/export/server/
scp /etc/profile root@node3:/etc/
```

使生效

```shell
source /etc/profile
```
