layout: post
title: "Python操作MySQL数据库"
date: 2019-05-13 11:00:00
author: ikangming
categories: Python
tags: 
    - 接口测试
top: false
cover: false
---
###  一、写在前头
> 1. DoMysql类完成对MySQL数据库：fetch_one跟fetch_all操作
> 2. fetch_one：返回单个的元组（只取最上面的第一条结果），也就是一条记录(row)，如果没有结果 , 则返回 None
> 3. fetch_all：返回多个元组，即返回多条记录(rows),如果没有结果,则返回 ()
> 4. 配置文件：将host，user，password，port记录在配置文件中，方便管理
> 5. 如无例外，本博客都以Python3.X编写

### 二、安装并导入pymysql模块
`pip install pymysql`
> Python操作MySQL数据库有三种方法
> 1. pymysql: 本文使用该模块
> 2. mysqldb：只能适用于Python2.7
> 3. mysql.connector：

### 三、读取配置文件的数据
> 读取config_file里的内容

``` python
import configparser
from ALA_WeChat_1.common import contants	# 见另一篇文章

class ReadBaseConfig:
    """
    完成读取任一配置文件的读取
    """

    def __init__(self):
        self.config = configparser.ConfigParser()
        self.config.read(contants.config_file)

    def get(self, section, option):
        return self.config.get(section, option)



conf = ReadBaseConfig()     # 读取配置文件实例化
```

### 四、代码如下
> 1. 完成对MySQL数据库的数据操作
> 2. 想要查询的SQL语句，也可以保存在excel中或者配置文件中

``` python
import pymysql
from ALA_WeChat_1.common.config import conf


class DoMysql:
    """
    完成对MySQL数据库的数据操作
    """

    def __init__(self):
        host = conf.get('database', 'host')
        user = conf.get('database', 'user')
        password = conf.get('database', 'password')
        port = int(conf.get('database', 'port'))
        # 创建连接
        self.mysql = pymysql.connect(host=host, user=user, password=password, port=port, charset='utf8')
        # 设置返回字典
        self.cursor = self.mysql.cursor(pymysql.cursors.DictCursor)  # 创建游标

    def fetch_one(self, sql):
        self.cursor.execute(sql)
        self.mysql.commit()
        return self.cursor.fetchone()  # 返回一条数据（以元组形式）

    def fetch_all(self, sql):
        self.cursor.execute(sql)
        return self.cursor.fetchall()  # 返回多条数据：元组里面嵌套元组

    def close(self):
        self.cursor.close()  # 关闭游标
        self.mysql.close()  # 关闭连接


if __name__ == '__main__':
    mysql = DoMysql()
    result1 = mysql.fetch_all("要查询的SQL语句")
    print('result:', result1)
    print('type(result1):', type(result1))
    mysql.close()

```