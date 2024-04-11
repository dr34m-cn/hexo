---
title: 前端下载二进制文件
date: 2020-11-30 09:28:30
tags: 前端
index_img: /assets/headImg/js.png
---

假设你从后台获取到的二进制数据为`res`，则

<!--more-->

```javascript
var eleLink = document.createElement('a');
eleLink.download = '导出.xls';
eleLink.style.display = 'none';
// 二进制内容转变成blob地址
var blob = new Blob([res]);
eleLink.href = URL.createObjectURL(blob);
// 触发点击
document.body.appendChild(eleLink);
eleLink.click();
// 然后移除
document.body.removeChild(eleLink);
```

如果发现乱码，可能是请求头问题，在请求头部加上

```javascript
responseType: 'arraybuffer'
```

就能指定返回为二进制流