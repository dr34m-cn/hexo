---
title: python实现微信jsapi签名
date: 2021-06-24 16:12:39
tags: [编程,Python]
index_img: /assets/headImg/python.png
---

* 以下完整演示了获取access_token，获取ticket，最后签名返回前端全过程。

<!--more-->

* requirements.txt

```
requests==2.23.0
redis==3.5.3
tornado==6.0.4
```

* 安装依赖

```shell
pip install -r requirements.txt
```

* 调用，其中xxx改成当前前端页面地址，不要url编码

```
http://localhost:8060/getApi?url=xxx
```

* 程序，注意，程序没有对可能的错误做处理（因为几乎不会出错，如果出错，九成是因为ip白名单没有配置）

```python
import redis
import requests
import json
import hashlib
import time
import random
import string

from tornado.web import Application, RequestHandler
from tornado.ioloop import IOLoop

# 公众号id与秘钥
appId = 'xxx'
appSecret = 'xxx'

# 缓存的access_token与ticket在redis中的key
ack = 'test_access_token'
tik = 'test_ticket'

# redis主机和密码
redisHost = 'xxx'
redisPassword = 'xxx'

r = redis.Redis(host=redisHost, port=6379, password=redisPassword, decode_responses=True)


def getTicket():
    if r.get(tik) is None:
        if r.get(ack) is None:
            params_ack = {'grant_type': 'client_credential', 'appid': appId, 'secret': appSecret}
            access_token_data = requests.get('https://api.weixin.qq.com/cgi-bin/token', params=params_ack).text
            access_token = json.loads(access_token_data)['access_token']
            r.set(ack, access_token, ex=7100)
        else:
            access_token = r.get(ack)
        params_tik = {'access_token': access_token, 'type': 'jsapi'}
        ticket_data = requests.get('https://api.weixin.qq.com/cgi-bin/ticket/getticket', params_tik).text
        ticket = json.loads(ticket_data)['ticket']
        r.set(tik, ticket, ex=7100)
    else:
        ticket = r.get(tik)
    return ticket


def getSignature(url):
    ran_str = ''.join(random.sample(string.ascii_letters + string.digits, 8))
    timestamp = int(time.time())
    params = {'noncestr': ran_str, 'jsapi_ticket': getTicket(), 'timestamp': timestamp, 'url': url}
    stringA = ''
    # 参数排序与拼接
    ks = sorted(params.keys())
    for k in ks:
        stringA += k + "=" + str(params[k]) + "&"
    stringSignTemp = stringA[:-1]

    # sha1
    hash_sha1 = hashlib.sha1(stringSignTemp.encode('utf8'))
    result_string = {
        'debug': True,
        'appId': appId,
        'timestamp': timestamp,
        'nonceStr': ran_str,
        'signature': hash_sha1.hexdigest(),
        'jsApiList': ['updateAppMessageShareData', 'updateTimelineShareData']
    }
    return json.dumps(result_string)


class getResult(RequestHandler):
    def get(self):
        url = self.get_argument('url')
        self.write(getSignature(url))


if __name__ == "__main__":

    app = Application([(r"/getApi", getResult)])

    port = 8060

    app.listen(port)

    print("\n\n=============================================================")
    print(" (♥◠‿◠)ﾉﾞ server run success at: http://0.0.0.0:%s ლ(´ڡ`ლ)ﾞ" % str(port))
    print("=============================================================\n\n")

    IOLoop.current().start()

```

* 在请求微信服务器时传自定义参数可以实现更多功能，详见[官方文档](https://developers.weixin.qq.com/doc/offiaccount/OA_Web_Apps/JS-SDK.html)

