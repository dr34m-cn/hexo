---
title: python - pip换源
date: 2020-11-16 17:06:23
tags: [运维,Python]
---

* windows直接在user目录中创建一个pip目录，如：C:\Users\xx\pip，新建文件pip.ini，内容如下

```ini
[global]
index-url = https://mirrors.aliyun.com/pypi/simple/
```

<!--more-->

linux修改 ~/.pip/pip.conf (没有就创建一个)， 内容同上

其他源

* 中国科技大学 `http://pypi.mirrors.ustc.edu.cn/simple/`

* 豆瓣 `http://pypi.douban.com/simple/`

* 清华 `https://pypi.tuna.tsinghua.edu.cn/simple/`

* 中国科学技术大学`http://pypi.mirrors.ustc.edu.cn/simple/`