---
title: 删除node_modules重新安装
date: 2021-03-18 14:48:05
tags: [编程,前端]
index_img: /assets/headImg/nodejs.png
---

* 1.关闭项目，终端中进入项目目录

<!--more-->

* 2.安装rimraf

```bash
npm install -g rimraf
```

* 3.删除 node_modules 包和 package-lock.json 文件

```sh
rimraf node_modules
rimraf package-lock.json
```

* 4.清除缓存

```shell
npm cache clear --force
```

* 5.重新安装依赖包

```shell
npm install
```

* 6启动项目

### 另外，请不要在任何情况、任何项目中使用cnpm，可能导致各种意想不到甚至无法修复的问题，如果嫌npm慢，可以换淘宝源来解决

```shell
npm config set registry https://registry.npmmirror.com/
```

