---
title: Python http.server 本地服务支持跨域
date: 2022-02-24 17:34:09
tags: [Python,运维]
index_img: /assets/headImg/python.png
---

# 目标

Python 使用http.server启动本地服务支持跨域

<!--more-->

# 实现

### 1. 创建start.py，代码如下

```python
#!/usr/bin/env python
try:
    # Python 3
    from http.server import HTTPServer, SimpleHTTPRequestHandler, test as test_orig
    import sys
    def test (*args):
        test_orig(*args, port=int(sys.argv[1]) if len(sys.argv) > 1 else 8000)
except ImportError: # Python 2
    from BaseHTTPServer import HTTPServer, test
    from SimpleHTTPServer import SimpleHTTPRequestHandler
 
class CORSRequestHandler (SimpleHTTPRequestHandler):
    def end_headers (self):
        self.send_header('Access-Control-Allow-Origin', '*')
        SimpleHTTPRequestHandler.end_headers(self)
 
if __name__ == '__main__':
    test(CORSRequestHandler, HTTPServer)
```

### 2. 运行start.py，端口为8000

```shell
python start.py 8000
```

### 3. windows建立脚本一键启动

在当前目录下新建`runServer.bat`，填入第二步中的命令并保存，之后双击脚本即可运行

> 参考[python3 http.server 本地服务支持跨域](https://www.cnblogs.com/zyh1994/p/11353214.html)

