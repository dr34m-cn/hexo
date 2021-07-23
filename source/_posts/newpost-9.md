---
title: pip 安装 tensorflow MemoryError
date: 2021-01-18 16:11:46
tags: [编程,Python]
---

原因是内存不够了，可以添加`--no-cache-dir`禁用缓存，例如：

```
pip install --no-cache-dir tenstoflow==2.2.0
```

