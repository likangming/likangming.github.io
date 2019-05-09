layout: post
title: "接口测试中cookies的复用"
date: 2019-03-01 14:47:00
author: ikangming
categories: Python
tags: 
    - 接口测试
top: false
cover: false
---
---
在对接口做测试的时候，一个接口请求参数后所产生的cookies，很多时候会作为下一个接口的数据依赖。本篇则从完成接口文档中注册，登录，充值的调用，其中充值接口支持传cookies参数。

### 前提准备
Python version：3.7
Ida：Pycharm

### 方法一：在一个接口请求中输入上一个接口的cookies信息
``` bash
class HttpRequest:
    """
    使用这类的request方法去完成不同的http请求，并且返回相应结果
    """

    def http_request(self, method, url, data, json=None, cookies=None):
        method = method.upper()  # 将method强制转成大写
        if method == 'GET':
            resp = requests.get(url, params=data, cookies=cookies)
        elif method == 'POST':
            if json:
                resp = requests.post(url, json=data, cookies=cookies)
            else:
                resp = requests.post(url, data=data, cookies=cookies)
        else:
            resp = None
            print('Un-support method')
        return resp
```

``` bash
# 操作代码
if __name__ == '__main__':
    params = {'mobilephone': 'XXX', 'pwd': 'XXX'}
    url_register = 'http://xxx.xxx'
    url_login = 'http://xxx.xxx'
    url_recharge = 'http://xxx.xxx'
    httprequest = HttpRequest()		# 实例化
    # 调用登录接口
    resp = httprequest.http_request('post', url_login, data=params)
    # 调用充值接口
    params = {'mobilephone': 'XXX', 'amount': '1500'}
    resp = httprequest.http_request('post', url_recharge, data=params, cookies=resp.cookies)
```

### 方法二：直接调用requests的sessions
``` bash
class HttpRequest2:
    """
    使用这类的request方法去完成不同的http请求，并且返回相应结果
    """
    def __init__(self):
        # 打开一个session
        self.session = requests.sessions.session()

    def http_request(self, method, url, data):
        method = method.upper()
        if method == 'GET':
            resp = self.session.request(method=method, url=url, params=data)
        elif method == 'POST':
            if json:
                resp = self.session.request(method=method, url=url, json=json)
            else:
                resp = self.session.request(method=method, url=url, data=data)
        else:
            resp = None
            print('Un-support method')
        return resp
		
	def session_close(self):
        self.session.close()    # 每执行一次session，都要将它关闭
```
``` bash
# 操作代码
if __name__ == '__main__':
    params = {'mobilephone': '156XXX', 'pwd': 'XXX'}
    url_register = 'http://xxx.xxx'
    url_login = 'http://xxx.xxx'
    url_recharge = 'http://xxx.xxx'
    httprequest = HttpRequest2()
    # 调用登录接口
    resp = httprequest.http_request('post', url_login, data=params)
    print(resp.text)
    # 调用充值接口
    params = {'mobilephone': '156XXX', 'amount': '1500'}
    resp = httprequest.http_request('post', url_recharge, data=params)
    print(resp.text)
```
#### 总结：方法一是调用充值接口时，输入登录的cookies信息，方法二是调用Session对象，Session每执行一个对象，cookies信息都有保留在当前Session，所以可以调用。明白其中的原理，用哪种方式都可以。