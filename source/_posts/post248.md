---
title: centos中jenkins配置环境变量
date: 2020-11-03 18:22:55
tags: [运维,持续集成]
---

起由：在Jenkins配置脚本期望执行npm命令，但实际没有执行，也没有报错
<!--more-->

### 1.获取系统环境变量

```sh
echo $PATH
```

得到如下，复制它
```
/root/.pyenv/shims:/root/.pyenv/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/root/bin
```

### 2.配置jenkins

系统管理-系统配置-全局属性-环境变量-新增-粘贴-保存  
如图

![248-1.jpg](/imgs/248/248-1.png)