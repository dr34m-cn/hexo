---
title: child_process exec 中文乱码 Windows&Linux 解决方法
date: 2022-08-25 10:29:58
tags: [前端]
index_img: /assets/headImg/nodejs.png
---

# 目标

在Windows&Linux中，Mac理论也可以，执行child_process exec时，中文可以正常显示。

<!--more-->

# 实现

### 1. 安装`iconv-lite`

```shell
npm install iconv-lite -S
```

### 2. 具体代码

`runExec.js`内容

```js
const exec = require('child_process').exec;
const os = require("os");
const iconv = require('iconv-lite');
const encoding = os.platform() == 'win32' ? 'cp936' : 'utf-8';
const binaryEncoding = 'binary';
// 执行命令并获取结果
const runExec = (cmd) => {
    return new Promise((resolve, reject) => {
        exec(cmd, { encoding: binaryEncoding }, function(error, stdout, stderr) {
            if (error) {
                reject(iconv.decode(new Buffer(error.message, binaryEncoding), encoding));
            } else {
                resolve(iconv.decode(new Buffer(stdout, binaryEncoding), encoding));
            }
        });
    })
}
export default runExec
```

调用方

```js
import runExec from 'runExec.js'
runExec('echo "中文测试"').then(res => {
    console.log('执行成功', res);
}).catch(err => {
    console.log('执行失败', err);
})
```

亲测在`Windows 11`和`Kali Linux`下都能正常显示中文，其他系统如有不支持，只需更改`encoding`即可