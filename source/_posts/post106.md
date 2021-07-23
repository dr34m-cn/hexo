---
title: FBCTF汉化简体中文免费下载，FBCTF更新缓存代码
date: 2017-03-17 22:40:52
tags: [分享,汉化]
---

[下载中文汉化版](http://d52.pw/fb)

### 安装方法

### 已经安装成功平台，更新代码

1.  点击上方链接下载并解压出lang_zh-cn.php
2.  上传到服务器根目录开始的
<!--more-->
```
    /var/www/fbctf/src/language/
```
目录下
3.  重新编译(有人称之为更新缓存)
```sh
    sudo rm /var/cache/hhvm/hhvm.hhbc
    sudo hhvm-repo-mode enable "/var/www/fbctf"
    sudo chown www-data:www-data /var/cache/hhvm/hhvm.hhbc
    sudo service hhvm start
    sudo service nginx restart
```
### 首次安装

1.  在从github下载源码到服务器之后，开始安装FBCTF之前，点击上边链接下载并解压出lang_zh-cn.php
2.  放到你下载的`fbctf/src/language/`
目录下，之后安装FBCTF
其他说明：因为“lang_zh-cn.php”和“lang_zh-tw.php”在后台管理中都显示为“中文/中文”，但是有一个简体有一个繁体，会造成不必要的麻烦，为避免这种情况，不需要繁体中文的用户可以删掉“lang_zh-tw.php”
