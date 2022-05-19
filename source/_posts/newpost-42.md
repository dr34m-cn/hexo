---
title: python批量转化doc到docx
date: 2022-05-19 15:25:38
tags: Python
index_img: /assets/headImg/python.png
---

# 目标

通过python批量转换doc文档为docx文档

<!--more-->

# 实现

## 一、代码

### windows下

```python
import os
from win32com import client as wc
word = wc.Dispatch("Word.Application")
path = os.getcwd()
for file in os.listdir(path):
    if file.endswith(".doc"):
        try:
            file_path = path + '\\' + file
            doc = word.Documents.Open(file_path)
            doc.SaveAs("{}x".format(file_path), 12)
            print("成功:    " + file + "   ->  " + file + "x")
        except Exception as e:
            print("失败:    " + file + "   ->  " + file + "x")
        finally:
            doc.Close()
word.Quit()
input("输入回车结束...")
```

### Linux下（未经测试，失败烦请告知）

```python
import os
import subprocess
path = os.getcwd()
for file in os.listdir(path):
    if file.endswith(".doc"):
        try:
            file_path = path + '/' + file
            subprocess.check_output(["soffice","--headless","--invisible","--convert-to","docx",file_path,"--outdir",path + '/'])
            print("成功:    " + file + "   ->  " + file + "x")
        except Exception as e:
            print("失败:    " + file + "   ->  " + file + "x")
input("输入回车结束...")
```



## 二、使用

将以上内容写入main.py移动到需要转换的文档目录，运行`python main.py`，即可将当前目录下所有doc文件转docx