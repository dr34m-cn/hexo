---
title: JS计算字节大小，把字节转换为KB/MB/GB/TB等
tags:
  - JavaScript
  - 前端
index_img: /assets/headImg/nodejs.png
date: 2024-04-10 17:24:35
---

# 目标

通过JS函数，把诸如`1024`转为`1KB`

<!--more-->

# 实现

直接上方法

```js
function calcByte(val) {
    var unitList = ['B', 'KB', 'MB', 'GB', 'TB'];
    for (var i = 0; i < unitList.length; i++) {
        if (val < 1024 ** (i + 1)) {
            return (val / (1024 ** i)).toFixed(2) + unitList[i];
        }
    }
}
```

入参为字节数值，如果不够，在`TB`后边接着加即可，其中`**`表示次方，如2的3次方表示为`2**3`