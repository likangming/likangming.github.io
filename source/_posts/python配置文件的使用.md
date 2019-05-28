layout: post
title: "python配置文件的使用"
date: 2019-05-10 15:00:00
author: ikangming
categories: Python
tags: 
    - 接口测试
top: false
cover: false
---
### 一、什么是配置文件？为什么要做配置文件？
> 将所有的代码和配置都变成模块化可配置化，这样就提高了代码的重用性，不再每次都去修改代码内部，这个就是我们逐步要做的事情，可配置化

### 二、python中的ConfigParser类
``` python
from configparser import ConfigParser
```
> configparser是python自带的模块，用法如下：
> 1. 创建ConfigParser对象。
> 2. 调用read()函数打开配置文件
> 3. 常用配置函数如下：

函数名 | 说明
--- | ---
sections() | 得到所有的 section，并以列表的形式返回
options(section) | 得到该 section 的所有 option (key值)
items(section) | 得到该 section 的所有键值对
get(section, option) | 得到 section 中 option 的值，返回为 string 类型，指定标签下面的 key 对应的 value 值
getint(section, option) | 得到 section 中的 option 值，返回为 int 类型
add_section() | 往配置文件中添加 section
set(section, name, value) | 在 section 下设置 name=value

### 三、实例
> 1. 新建config.py文件

``` python
#!/usr/bin/env python
# _*_coding:utf-8_*_
"""
@Time     : 2019/5/10 13:59
@Author   : Damon
@Email    : kangming40@163.com
@File     : config.py
@Software : PyCharm
"""
import configparser
from ALA_WeChat_1.common import contants


class ReadConfig:
    """
    完成配置文件的读取
    """

    def __init__(self):
        self.config = configparser.ConfigParser()  # 初始化类
        self.config.read(contants.global_file)  # 先加载global
        switch = self.config.getboolean('switch', 'on')  # 通过判断switch的值，来确定使用哪个环境的配置
        if switch:      # 开关打开的时候，使用test的配置
            self.config.read(contants.test_file, encoding='utf-8')
        else:       # 开关关闭的时候，使用issue的配置
            self.config.read(contants.issue_file, encoding='utf-8')

    def get(self, section, option):
        return self.config.get(section, option)


config = ReadConfig()       # 1.实例化供调用
# if __name__ == '__main__':        # 2.练习的时候
#     config = ReadConfig()
#     print(config.get('api', 'pre_url'))
```

> 2. 新建contants.py文件（我是将测试用例的相对路径，多个配置文件的相对路径写在这个py文件里，方便我管理）

``` python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# Author:Damon
# Created time:2019/5/10 14:35
# Filename:contants.py
import os
base_dir = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))      # 当前项目的路径
case_file = os.path.join(base_dir, 'data', 'cases.xlsx')        # 测试用例的路径
global_file = os.path.join(base_dir, 'conf', 'global.conf')       # global配置文件
online_file = os.path.join(base_dir, 'conf', 'online.conf')       # 阿里云环境配置文件
test_file = os.path.join(base_dir, 'conf', 'test.conf')       # 测试环境配置文件
issue_file = os.path.join(base_dir, 'conf', 'issue.conf')     # 预发布环境配置文件
```

> 3.conf配置文件书写格式：

``` python
[section]
option = value
```