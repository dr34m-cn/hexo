---
title: 内湾穿透工具frp的常用配置
date: 2023-09-07 14:17:14
tags: [经验, 运维]
index_img: /assets/headImg/frp.png
---

# 目标

通过简化的配置，实现内网穿透

<!--more-->

# 实现

## 一、下载对应的工具

前往官方下载 [https://github.com/fatedier/frp/releases](https://github.com/fatedier/frp/releases)

## 二、编辑配置文件

### 1.服务端frps.ini

```ini
[common]
# 服务端口
bind_port = 7000

# 控制面板地址
dashboard_addr = 0.0.0.0
# 控制面板端口
dashboard_port = 7001
# 控制面板用户名
dashboard_user = xxxxxx
# 控制面板密码
dashboard_pwd = yyyyyyyy


# 鉴权方式
authentication_method = token
# 鉴权秘钥
token = zzzzzzzzz
# 日志文件
log_file = /root/frp/frps.log
# 日志等级
log_level = info

# 允许的端口范围
allow_ports = 7002-7999
```

### 2.客户端frpc.ini

```ini
[common]
# 服务器地址
server_addr = 111.111.111.111
# 服务端口
server_port = 7000
# 鉴权秘钥
token = zzzzzzzzz
# 本机用户名，可随意填写，但不要和别人重复，用于区分和避免重名
user = user001

[ssh] # 这个名字随意，但不要重复，最好有明确意义方便一眼认出
# 类型
type = tcp
# 本地ip，也可设为192.168.0.1等本地可以访问到的地址
local_ip = 127.0.0.1
# 本地端口号
local_port = 22
# 远程端口号，注意，如果在服务端配置了【allow_ports】，这里端口号需要在范围内
remote_port = 7022
```

## 三、启动

### 1.直接使用命令启动

```shell
./frps -c frps.ini # 服务端

./frpc -c frpc.ini # 客户端
```

这种方法一旦关闭窗口则服务也会停止，或者你可以用下边的方法创建为服务

### 2.创建为服务（CentOS7）

以服务端为例，客户端同理

```shell
vim /usr/lib/systemd/system/frps.service
```

假定你的frp可执行文件和配置都放在`/root/frp/`目录，否则请修改下面的路径为实际

```systemd
[Unit]
Description=frps
After=network.target syslog.target

[Service]
Type=simple
ExecStart=/root/frp/frps -c /root/frp/frps.ini

[Install]
WantedBy=multi-user.target
```

常用命令

```shell
# 重载配置文件（编辑或新增服务后执行）
systemctl daemon-reload
# 设置开机自启动
systemctl enable frps
# 启动
systemctl start frps
# 停止
systemctl stop frps
# 重启
systemctl restart frps
# 查看状态
systemctl status frps
```