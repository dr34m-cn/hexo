---
title: 基于firewalld端口转发
date: 2021-02-08 15:07:53
tags: 运维
index_img: /assets/headImg/linux.png
---

## 1.首先确认firewalld已经安装

```shell
firewall-cmd --state
```

如果输出不**是running**，则可能没安装或者没启动，没启动会提示`not running`

<!--more-->

* 没安装，通过`yum install -y firewalld`安装

* 安装后，或没启动，通过以下命令配置开机自动启动并立即启动

  ```shell
  systemctl enable firewalld
  systemctl start firewalld
  ```

  

## 2.配置转发

* 本实例假设你要把本地8080端口转发到目标服务器88.88.88.88的443端口，同时转发TCP和UDP

```shell
echo 'net.ipv4.ip_forward = 1' >> /etc/sysctl.conf
sysctl -p
firewall-cmd --permanent --add-masquerade
# 8080可以改成其他端口
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --permanent --add-port=8080/udp
# 8080和上面保持一致，目标ip和端口都可以修改
firewall-cmd --permanent --add-forward-port=port=8080:proto=tcp:toaddr=88.88.88.88:toport=443
firewall-cmd --permanent --add-forward-port=port=8080:proto=udp:toaddr=88.88.88.88:toport=443
firewall-cmd --reload
```