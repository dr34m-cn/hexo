---
title: CentOS安装并使用conda
date: 2022-06-24 09:40:50
tags: [运维]
index_img: /assets/headImg/conda.png
---

# 一、简介

[conda](https://docs.conda.io/en/latest/)是一款适用于任何语言（Python、R、Ruby、Lua、Scala、Java、JavaScript、C/C++、Fortran 等）的包、依赖项和环境管理工具。

<!--more-->

# 二、安装

### 1. 下载

[https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/](https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/)

### 2. 依赖

```sh
yum -y install gcc libsm6 libXext libSM libXrender
```

### 3. 赋权

```sh
chomd 777 Anaconda3xxxx.sh
```

### 4. 执行

```sh
bash Anaconda3xxxx.sh
```

### 5. 添加到环境变量

```sh
vi /etc/profile
# 结尾添加 export PATH=$PATH:/root/anaconda3/bin
source /etc/profile
```

# 三、使用

### 1. 创建名为env_name的Python3.6环境

```sh
conda create -n env_name python=3.6 pip
```

### 2. 使用env_name环境

```sh
conda activate env_name
```

### 3. 退出环境

```sh
conda deactivate
```

### 4. 复制某个虚拟环境

```sh
conda create --n new_env_name --clone old_env_name
```

### 5. 删除某个环境

```sh
conda remove --n env_name --all
```

### 6. 查看当前所有环境

```sh
conda info --envs
# 或者
conda env list
```

### 7. 查看当前虚拟环境下的所有安装包

```sh
conda list # 需进入该虚拟环境
# 或者
conda list -n env_name
```

### 8. 安装或卸载包(进入虚拟环境之后）

```sh
conda install xxx
conda install xxx=版本号 # 指定版本号
conda install xxx -i 源名称或链接 # 指定下载源
conda uninstall xxx
```

### 9. 分享虚拟环境

```sh
conda env export > environment.yml # 导出当前虚拟环境
conda env create -f environment.yml # 创建保存的虚拟环境
```

### 10. 升级

```sh
conda update conda
conda update anaconda
```

### 11. 卸载

```sh
rm -rf anaconda
```

> 参考[conda文档](https://docs.conda.io/en/latest/)、[Conda常用命令](https://blog.csdn.net/zhognsc08/article/details/122735811)
