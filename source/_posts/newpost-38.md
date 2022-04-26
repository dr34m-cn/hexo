---
title: python归一化数据
date: 2022-03-31 20:08:58
tags: [Python, 机器学习, TensorFlow]
index_img: /assets/headImg/tensorflow.png
---

# 目标

使用python对pandas拿到的数据进行归一化，方便用于训练或者预测

<!--more-->

# 实现

数据来源：

```python
import pandas as pd

dataset = pd.read_csv("data/boston.csv")
```

#### 1. 均值方差归一化（推荐）

适用于没明显边界，有可能存在极值的

```python
stats = dataset.describe().transpose()
def norm(x):
  return (x - stats['mean']) / stats['std']
normed_data = norm(dataset)
```

#### 2. 最值归一化

适用于有边界为题的，如果存在极端的极值，会影响归一化的结果

```python
def norm(x):
  return (x - x.min()) / (x.max() - x.min())
normed_data = norm(dataset)
```

**经过实际测试，采用第一种方法预测的数据更为准确（表现为mae更小，预测值与真实值更吻合。当然在特别的数据下，你可能得到截然相反的结果），所以更推荐第一种。**

**但当数据只有一条或者很少时，可能拿不到方差，这时可以采用第二种**

> 参考：[数据归一化(附简单实现和sklearn简单处理数据)](https://blog.csdn.net/qq_39871991/article/details/106941740)