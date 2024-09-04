---
title: 一个上手即用的通用公众号/小程序/h5/app框架
date: 2022-06-10 18:50:03
updated: 2022-06-17 11:13:00
tags: [前端]
index_img: /assets/headImg/ui.png
---

源码地址：[https://github.com/dr34m-cn/uniapp-demo](https://github.com/dr34m-cn/uniapp-demo)

框架后期或有更新，请以源码中`README.md`文档为准

本通用框架基于`uniapp`与`uView UI 2.0.31`，封装了日常开发中最常用的接口请求、数据中心、环境配置等操作，上手即用。

## 1. 运维

### 1.1 构建

```shell
# H5 生产
npm run build

# H5 dev
npm run build:dev

# 微信小程序
npm run build:mp-weixin
```

其他平台构建，例如快应用、支付宝小程序等详见[uniapp官方文档](https://uniapp.dcloud.net.cn/quickstart-cli.html#%E8%BF%90%E8%A1%8C%E3%80%81%E5%8F%91%E5%B8%83uni-app)

### 1.2 H5修改前缀

修改`/src/manifest.json`，例如修改前缀为`/h5`如下

```js
"h5" : {
        "title" : "标题",
        "router" : {
                "base" : "/h5/"
        }
}
```

更多配置详见[uniapp官方文档](https://uniapp.dcloud.net.cn/collocation/manifest.html)

### 1.3 配置文件

配置文件在`/src/config/index.js`

## 2. 开发

### 2.1 启动

```shell
# 安装依赖
npm install

# 运行
npm run serve
```

其他平台运行，例如快应用、支付宝小程序等详见[uniapp官方文档](https://uniapp.dcloud.net.cn/quickstart-cli.html#%E8%BF%90%E8%A1%8C%E3%80%81%E5%8F%91%E5%B8%83uni-app)

### 2.2 说明

#### 2.2.1 配置

通用配置请放在`/src/config/index.js`中，例如后端地址。

#### 2.2.2 数据中心

数据中心统一管理需要在全局使用的数据，可以临时存储（h5刷新页面、小程序/app退出等操作数据就会丢失）或者持久化存储。

在这里定义和存储的数据是全局动态响应的，这让您无需繁琐地写监听方法。

配置文件在`/src/store/index.js`，建议命名为`vuex_`开头，以便与页面数据区分避免冲突。

##### 2.2.2.1 定义

###### 2.2.2.1.1 临时存储

直接在`/src/store/index.js`中定义，如下边的`vuex_tmp`

```js
const store = new Vuex.Store({
    state: {
        vuex_token: lifeData.vuex_token ? lifeData.vuex_token : null,
        vuex_tmp: null
    },

    ...

})
```

###### 2.2.2.1.2 持久化存储

在`/src/store/index.js`中定义，并写入`saveStateKeys`中，如下边的`vuex_token`，定义的时候格式为`vuex_token: lifeData.vuex_token ? lifeData.vuex_token : null`

```js
// 需要永久存储，且下次APP启动需要取出的，在state中的变量名
let saveStateKeys = ['vuex_token'];

...

const store = new Vuex.Store({
    state: {
        vuex_token: lifeData.vuex_token ? lifeData.vuex_token : null,
        vuex_tmp: null
    },

    ...

})
```

##### 2.2.2.2 使用

###### 2.2.2.2.1 取值

* 页面`<template>`中取值`{{ vuex_token }}`
* js代码（包括`App.vue`页面）`<script>`中取值`this.vuex_token`
* 拦截器等通用组件中（须获取上下文vm）取值`vm.vuex_token`

###### 2.2.2.2.2 设值

可以设定值的类型为`基本数据类型`和`可以进行序列化的对象/列表等`。

```js
// 字符串
this.$u.vuex('vuex_token', "xxxxx");
// 对象
this.$u.vuex('vuex_token', {
    id: 1,
    key: 'token',
    value: 'xxxxx'
});
```

#### 2.2.3 接口

接口基于[luch-request](https://www.quanzhan.co/luch-request/)，`uView`对其简单封装，本示例使用的是`uView`封装后的，对最常用的场景做了说明，更详细的文档见[uView的http请求](https://www.uviewui.com/js/http.html)

##### 2.2.3.1 统一拦截器

接口拦截器在`/src/utils/request.js`中，可以根据需要自行修改；

对于请求：

* 凡是不以`/login`开头，且当前已有`token`的（数据中心`vuex_token`字段值非`null`），将自动在请求头`header.token`存入已有的`token`值；

对于响应：

* 响应值`response.statusCode`非`200`的，将拦截，弹框提示并抛出异常，可在接口`catch`中捕获；
* 响应数据存在`response.data.code`字段的，判断`code`值：
  * 值为`401`将拦截并抛出异常，可在接口`catch`中捕获，建议在拦截器中统一处理；
  * 值非`200`且非`401`将拦截，弹框提示并抛出异常，可在接口`catch`中捕获；
  * 值为`200`的，将返回`response.data`，可在接口`then`中获取；
* 响应数据不存在`response.data.code`字段的，将返回`response.data`，可在接口`then`中获取。

##### 2.2.3.2 api集中管理

提示：get请求第一个参数是配置，post请求第一个参数是请求值，第二个才是配置

在`/src/api/`目录下新建`api.js`文件，内容如下

```js
const http = uni.$u.http

// get请求
export const getMenu1 = () => http.get(`/ebapi/public_api/index`)

// get请求
export const getMenu2 = (id) => http.get(`/ebapi/public_api/index/${id}`)

// get请求
export const getMenu3 = (params) => http.get('/ebapi/public_api/index', {params})

// get请求（带header等配置）
export const getMenu4 = (params) => http.get('/ebapi/public_api/index', {
    params,
    header: {
        token: '1111'
    }
})

// post请求
export const postMenu = (data) => http.post('/ebapi/public_api/index', data)

// post请求（带header等）
export const postMenu = (data) => http.post('/ebapi/public_api/index', data, {
    header: {
        token: '1111'
    },
    responseType: 'arraybuffer'
})

// 更多请求使用
uni.$u.http.get(url[, config])
uni.$u.http.post(url[, data[, config]])
uni.$u.http.delete(url[, data[, config]])
uni.$u.http.put(url[, data[, config]])
uni.$u.http.middleware(config)
uni.$u.http.request(config)
uni.$u.http.upload(url[, config])
uni.$u.http.head(url[, data[, config]])
uni.$u.http.connect(url[, data[, config]])
uni.$u.http.options(url[, data[, config]])
uni.$u.http.trace(url[, data[, config]])
```

##### 2.2.3.3 api使用

```js
import {
    getMenu2,
    getMenu3
} from '@/api/api.js';

getMenu2(1).then(res => {
    // 经过拦截器的处理，进入到这里的请求都是成功请求，无需考虑请求失败的情况
    console.log('res',res)
}).catch(err=>{
    // 多数情况下，不需要写catch，因为拦截器已经进行了弹窗提示等操作
    // 但当页面需要对错误进行处理时（例如关闭加载动画，取消按钮loading等），就需要在catch中操作
    console.log('err',err)
})

getMenu3({
    id: 1
}).then(
    ...
).catch(
    ...
)

postMenu({
    id: 1
}).then(
    ...
).catch(
    ...
)
```

#### 2.2.4 全局过滤器

##### 2.2.4.1 定义

全局过滤器定义在`/src/utils/filters.js`中，可参照其增加自己的过滤规则，常用定义方式如下

```js
// 0-男,1-女
const sexFilter = (value) => {
    let sexList = ['男', '女'];
    return sexList[value] ? sexList[value] : "--";
}

// xx-小学及以下，cz-初中，gz-高中及以上
const educationFilter = (value) => {
    switch (value) {
        case 'xx':
            return '小学及以下';
        case 'cz':
            return '初中';
        case 'gz':
            return '高中及以上';
        default:
            return '--';
    }
}

export default {
    sexFilter,
    educationFilter
}
```

##### 2.2.4.2 使用

因为已经在`main.js`中定义如下

```js
import filters from '@/utils/filters';
Object.keys(filters).forEach(k => Vue.filter(k, filters[k]));
```

所以页面中无需引入，如下直接使用

```html
<view>过滤器-性别：{{ sex | sexFilter }}</view>
```

效果：当`sex`为`0`，页面输出`男`；当`sex`为`1`，页面输出`女`

> 参考 [uView](https://www.uviewui.com/)、[uniapp](https://uniapp.dcloud.net.cn/)