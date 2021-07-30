---
title: Hexo + Github Actions 实现提交代码自动部署并通过FTP传到云服务器
date: 2021-07-23 17:41:16
tags: [运维,经验]
---

### 本文要求已经对Hexo有一点了解，并且知道怎么托管hexo到github，如果您刚接触Hexo，可以参考[Hexo安装配置并托管至github](https://blog.ctftools.com/2017/12/post233/)

### 如果不想依赖github，可以参考这套自建方案[centos下hexo + svn + jenkins实现博客自动部署](https://blog.ctftools.com/2020/11/post249/)、[svn提交后jenkins自动部署](https://blog.ctftools.com/2020/11/newpost-1/)

<!--more-->

**本文涉及所有私钥密码等都非常重要，一定要配置到github项目中的`Secrets`里，不要图省事**

## 一、新建github私有仓库保存hexo源码

不细说，仓库也可以公开，配置到`Secrets`里的信息是非常安全的

## 二、Github Actions配置

### 2.1 配置仓库私钥

进入存放源码的私有仓库，点击`Settings`-`Secrets`，`New repository secret`，新建名为`HEXO_DEPLOY_PRIVATE_KEY`的私钥，值为你的私钥，通常可以在`\.ssh\id_rsa`中的获取到。

### 2.2 配置Actions

点击私有仓库`Actions`-`New workflow`-` set up a workflow yourself`，内容编辑如下，把其中的`名字`和`邮箱`替换成你自己的

```yml
# workflow name
name: Hexo博客持续集成

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

如果需要传到国内云加速访问，常见的有两种方式：

#### 1.FTP方式(较慢，推荐下边的方法)

可以在上边的内容紧接着加上以下内容，具体配置参考[FTP-Deploy-Action](https://github.com/SamKirkland/FTP-Deploy-Action)，这个脚本在删除操作会报550错误，项目`Issues`也提到了这个问题，目前没有解决。


```yml
    - name: 部署Hexo到云服务器
      uses: SamKirkland/FTP-Deploy-Action@4.1.0
      with:
        server: ${{ secrets.FTP_SERVER }}
        username: ${{ secrets.FTP_USERNAME }}
        password: ${{ secrets.FTP_PASSWORD }}
        local-dir: ./public/
```

**ftp连接会比较慢，尤其是第一次，因为要全量上传，之后增量上传会快很多。**

还有**注意服务器要放行FTP被动模式端口**，否则能创建文件夹，但不能上传文件。被动模式端口可能是30000-40000，或者3000-4000，这个是可以在你的FTP服务端软件配置的，类似于`PassivePortRange 39000 40000`。

注意这需要在私有仓库，`Settings`-`Secrets`，`New repository secret`，新建相应secret，方法与上边新建`HEXO_DEPLOY_PRIVATE_KEY`一样，`FTP_SERVER`不用带`ftp://`之类的，直接填入`ftp.xxx.com`即可。

#### 2.SSH方式

SSH通常比FTP快很多，大概是几分钟和几秒钟的区别，具体配置参考[ssh-deploy](https://github.com/easingthemes/ssh-deploy)

```yml
    - name: 部署到云服务器
      uses: easingthemes/ssh-deploy@main
      env:
        SSH_PRIVATE_KEY: ${{ secrets.SERVER_SSH_KEY }}
        ARGS: "-avzr --delete"
        SOURCE: "./public/"
        REMOTE_HOST: ${{ secrets.SERVER_HOST }}
        REMOTE_USER: ${{ secrets.SERVER_USER }}
        TARGET: "/www/wwwroot/blog.ctftools.com/"
```

其中`SERVER_SSH_KEY`为SSH私钥，可以参考[设置 SSH 通过密钥登录](https://www.runoob.com/w3cnote/set-ssh-login-key.html)，教程中得到的`id_rsa`文件内容就是私钥。

如果想要部署到腾讯云COS，再配合CDN加速访问，可以参考[通过Github Actions部署静态网站到腾讯云COS，并自动刷新CDN](https://blog.ctftools.com/2021/07/newpost-20/)

也可以参考[我的项目中的配置](https://github.com/dr34-m/hexo/blob/master/.github/workflows/main.yml)，我的项目优先完成腾讯云COS部署，然后是托管到github，发布 Release（相当于存档）。

参考文章：[利用GitHub+Actions自动部署Hexo博客](https://blog.csdn.net/u012208219/article/details/106883054#comments_15417337)，[GitHub Actions 自动发布Hexo 并通过 FTP上传 到阿里云ECS](https://moeci.com/posts/github-actions-hexo-ftp/)，[githubActions部署文件到服务器](https://blog.csdn.net/qq_39846820/article/details/115422544)
