---
title: TensorFlow 预测出现NaN的一种可能以及解决方法
date: 2022-04-21 21:11:09
tags: [Python, 机器学习, TensorFlow]
index_img: /assets/headImg/tensorflow.png
---

## 现象

* 使用TensorFlow进行训练（`model.fit`）时，一切正常，并且对测试数据集的预测结果（`model.evaluate`）也表现正常，这时候保存模型
* 对训练集中单独一条数据，或者少量数据进行预测，预测结果出现`NaN`
* 如果对稍大量数据预测，则能正常出结果，但预测结果准确度低于预期
* 如果对大量数据预测，则结果准确度符合预期

<!--more-->

## 原因

经过分析，是由于训练和预测使用的归一化方法中使用的变量不一致导致的，训练和预测都是用下边的归一化方法

```python
train_stats = train_dataset.describe().transpose()
train_stats_final = train_stats[['mean', 'std']]
def norm(x):
	return (x - train_stats_final['mean']) / train_stats_final['std']
```

但由于训练和预测的数据不一样，导致其`mean`和`std`值不同，尤其预测用数据量较少时，其值差别巨大，导致训练的模型不适用于预测时归一化出来的数据，最终预测结果出现`NaN`值

## 解决方法

在训练保存模型时，同时对训练数据的`mean`和`std`值保存，在预测时使用

### 1.保存

```python
train_stats = train_dataset.describe().transpose()
train_stats_final = train_stats[['mean', 'std']]
train_stats_final.to_csv('xxx.csv')
```

### 2.使用

```python
train_stats_final = pd.read_csv("xxx.csv", index_col=0)
```



