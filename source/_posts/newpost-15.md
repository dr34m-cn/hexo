---
title: VsCode开发Python常用配置
date: 2021-06-24 16:06:11
tags: [编程,Python]
index_img: /assets/headImg/python.png
---

```js
{
    "python.linting.flake8Enabled": true,
    "python.formatting.provider": "yapf",
    "python.formatting.yapfArgs": ["--style={column_limit=128}"],
    "python.linting.flake8Args": ["--max-line-length=248"],
    "python.linting.pylintEnabled": false
}
```

<!--more-->

以上采用yapf格式化代码，格式化后单行代码最大长度为128
