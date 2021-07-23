---
title: Hexo + Github Actions 实现提交代码自动部署
date: 2021-07-23 17:41:16
tags: 运维
---

### 本文要求已经对Hexo有一点了解，并且知道怎么托管hexo到github，如果您刚接触Hexo，可以参考[Hexo安装配置并托管至github](https://blog.ctftools.com/2017/12/post233/)

<!--more-->

## 一、新建github私有仓库保存hexo源码

不细说

## 二、Github Actions配置

### 2.1 配置仓库私钥

进入存放源码的私有仓库，点击`Settings`-`Secrets`，`New repository secret`，新建名为`HEXO_DEPLOY_PRIVATE_KEY`的私钥，值为你的私钥，通常可以在`\.ssh\id_rsa`中的获取到。

### 2.2 配置Actions

点击私有仓库`Actions`-`New workflow`-` set up a workflow yourself`，内容编辑如下，把其中的`名字`和`邮箱`替换成你自己的

```yml
# workflow name
name: Hexo Blog CI

# master branch on push, auto run
on: 
  push:
    branches:
      - master
      
jobs:
  build: 
    runs-on: ubuntu-latest 
        
    steps:
    # check it to your workflow can access it
    # from: https://github.com/actions/checkout
    - name: Checkout Repository master branch
      uses: actions/checkout@master 
      
    # from: https://github.com/actions/setup-node  
    - name: Setup Node.js 11.x 
      uses: actions/setup-node@master
      with:
        node-version: "11.x"
    
    - name: Setup Hexo Dependencies
      run: |
        npm install hexo-cli -g
        npm install
    
    - name: Setup Deploy Private Key
      env:
        HEXO_DEPLOY_PRIVATE_KEY: ${{ secrets.HEXO_DEPLOY_PRIVATE_KEY }}
      run: |
        mkdir -p ~/.ssh/
        echo "$HEXO_DEPLOY_PRIVATE_KEY" > ~/.ssh/id_rsa 
        chmod 600 ~/.ssh/id_rsa
        ssh-keyscan github.com >> ~/.ssh/known_hosts
        
    - name: Setup Git Infomation
      run: | 
        git config --global user.name '名字' 
        git config --global user.email '邮箱'
    - name: Deploy Hexo 
      run: |
        hexo clean
        hexo generate 
        hexo deploy

```

完成后点击右上角`Start commit`-`Commit new ile`，这个Actions是提交代码后触发，快去试试吧，提交后约需要1分钟部署，之后就能在你托管的页面看到最新的博客了



参考文章：[利用GitHub+Actions自动部署Hexo博客](https://blog.csdn.net/u012208219/article/details/106883054#comments_15417337)
