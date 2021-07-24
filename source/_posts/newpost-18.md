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
    - name: 检出仓库代码
      uses: actions/checkout@master

    # from: https://github.com/actions/setup-node
    - name: 安装Node.js 11.x 
      uses: actions/setup-node@master
      with:
        node-version: "11.x"

    - name: 安装Hexo依赖
      run: |
        npm install hexo-cli -g
        npm install

    - name: 配置部署私钥
      env:
        HEXO_DEPLOY_PRIVATE_KEY: ${{ secrets.HEXO_DEPLOY_PRIVATE_KEY }}
      run: |
        mkdir -p ~/.ssh/
        echo "$HEXO_DEPLOY_PRIVATE_KEY" > ~/.ssh/id_rsa
        chmod 600 ~/.ssh/id_rsa
        ssh-keyscan github.com >> ~/.ssh/known_hosts

    - name: 配置GIT信息
      run: |
        git config --global user.name '名字'
        git config --global user.email '邮箱'

    - name: 部署Hexo到Github
      run: |
        hexo clean
        hexo generate
        hexo deploy

```

完成后点击右上角`Start commit`-`Commit new ile`，这个Actions是提交代码后触发，快去试试吧，提交后约需要1分钟部署，之后就能在你托管的页面看到最新的博客了

如果需要通过ftp传到国内云加速访问，可以在上边的内容紧接着加上以下内容，具体配置参考[FTP-Deploy-Action](https://github.com/SamKirkland/FTP-Deploy-Action)

**ftp连接国内会比较慢，尤其是第一次，因为要全量上传，之后增量上传会快很多。**

还有**注意服务器要放行FTP被动模式端口**，否则能创建文件夹，但不能上传文件。被动模式端口可能是30000-40000，或者3000-4000，这个是可以在你的FTP服务端软件配置的，类似于`PassivePortRange 39000 40000`。

```yml
    - name: 部署Hexo到云服务器
      uses: SamKirkland/FTP-Deploy-Action@4.1.0
      with:
        server: ${{ secrets.FTP_SERVER }}
        username: ${{ secrets.FTP_USERNAME }}
        password: ${{ secrets.FTP_PASSWORD }}
        local-dir: ./public/
```

注意这需要在私有仓库，`Settings`-`Secrets`，`New repository secret`，新建相应secret，方法与上边新建`HEXO_DEPLOY_PRIVATE_KEY`一样，`FTP_SERVER`不用带`ftp://`之类的，直接填入`ftp.xxx.com`即可。

参考文章：[利用GitHub+Actions自动部署Hexo博客](https://blog.csdn.net/u012208219/article/details/106883054#comments_15417337)，[GitHub Actions 自动发布Hexo 并通过 FTP上传 到阿里云ECS](https://moeci.com/posts/github-actions-hexo-ftp/)
