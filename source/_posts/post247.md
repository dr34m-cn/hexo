---
title: centos安装jenkins
date: 2020-11-03 17:58:28
tags: [运维,持续集成]
index_img: /assets/headImg/jenkins.png
---

## 一、安装jdk
首先确认系统是否安装jdk环境
```bash
java -version
```
<!--more-->
如果没安装会提示你`command not found`，使用下边的命令安装
```bash
yum install -y java
```
## 二、安装jenkins

### 1.添加Jenkins库到yum库并配置key，可以到 [jenkins官网](https://pkg.jenkins.io/redhat-stable/) 获取最新库和key
```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```

### 2.安装jenkins
```bash
yum install jenkins
```

### 3.修改jenkins默认端口号

jenkins默认端口号8080，如果要修改请
```bash
vim /etc/sysconfig/jenkins
```
找到`JENKINS_PORT="8080"`修改即可

### 4.启动jenkins

```bash
service jenkins start
```
* 停止和重启分别用下边的命令

```bash
service jenkins stop #停止
service jenkins restart #重启
```
* 安装成功后Jenkins将作为一个守护进程随系统启动

### 5.打开jenkins
浏览器输入服务器地址加上端口号，例如`http://190.168.1.2:8080/`

首次打开会让你输入密码，密码在
```bash
vim /var/lib/jenkins/secrets/initialAdminPassword
```
复制进去即可

之后可以默认安装推荐插件，配置用户名密码，安装完成。

### 6.更新jenkins

```bash
yum update jenkins
```

这种方式可能无法更新到最新的jenkins，需要手动在磁盘查找`jenkins.war`并替换

* 停止jenkins`service jenkins stop`

* `rpm -ql jenkins`找到`jenkins.war`，然后官网下载最新的替换

* 启动jenkins`service jenkins start`