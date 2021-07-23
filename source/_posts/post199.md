---
title: 搭建L(Linux)+A(Apache)+M(MySQL)+P(PHP)网站环境，并安装Discuz
date: 2017-06-16 21:17:32
tags: 运维
---

### 搭建LAMP环境：以下基于腾讯云镜像CentOS 6.8 64bit，本方法不适用于其他镜像

### 一、安装MySQL

#### 1.使用 yum 安装 MySQL：

    yum install mysql-server -y

#### 2.安装完成后，启动 MySQL 服务：

    service mysqld restart
<!--more-->
#### 3.配置账号密码

    /usr/bin/mysqladmin -u root password '9MiOskMW'

#### 4.将 MySQL 设置为开机自动启动：

    chkconfig mysqld on

### 二、安装Apache

#### 1.使用 yum 安装 Apache 组件：

    yum install httpd -y

#### 2.安装之后，启动 httpd 进程：

    service httpd start

#### 3.把 httpd 也设置成开机自动启动：

    chkconfig httpd on

### 三、安装PHP

#### 1.使用 yum 安装 PHP：

    yum install php php-fpm php-mysql -y

#### 2.安装之后，启动 PHP-FPM 进程：

    service php-fpm start

#### 3.启动之后，可以使用下面的命令查看 PHP-FPM 进程监听哪个端口

    netstat -nlpt | grep php-fpm

#### 4.把 PHP-FPM 也设置成开机自动启动：

    chkconfig php-fpm on

### 安装Discuz

#### 1.CentOS 6 没有Discuz 的 yum 源，所以我们需要下载一个Discuz 压缩包：

    wget http://download.comsenz.com/DiscuzX/3.2/Discuz_X3.2_SC_UTF8.zip

#### 2.下载完成后，解压这个压缩包

    unzip Discuz_X3.2_SC_UTF8.zip

#### 3.由于PHP默认访问 /var/www/html/ 文件夹，所以我们需要把upload文件夹里的文件都复制到 /var/www/html/ 文件夹

    cp -r upload/* /var/www/html/

#### 4.给 /var/www/html 目录及其子目录赋予权限

    chmod -R 777 /var/www/html

#### 4.重启 Apache

    service httpd restart

### 至此大功告成，通过浏览器访问服务器IP地址即可打开Discuz安装页面