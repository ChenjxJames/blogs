---
title: python3 使用 pymssql 连接 SQL Server
date: 2019-03-15
categories:
- Code
- Python
tags:
- pymssql
---

首先通过 pip 安装 pymssql 模块

如果有多个 python 版本并存注意区分

```bash
pip install pymssql
```

这里我写了一个简单的类以供参考。

<!-- more -->

```python
import pymssql
class ConnectSqlserver():
    def __init__(self, host="localhost", user="root", password="", db="test", charset='utf8'):
        # 打开数据库连接
        self.conn = pymssql.connect(host=host, user=user, password=password, database=db, charset=charset)
        # 使用 cursor() 方法创建一个游标对象 cursor
        self.cursor = self.conn.cursor()
    def __del__(self):
        self.conn.close()  # 关闭连接
    def run_sql(self, sql):
        # 插入数据, 修改数据， 数据库操作
        print(sql)
        try:
            self.cursor.execute(sql)
            self.conn.commit()
        except Exception as e:
            self.conn.rollback()
            print('Error: ', e)
        finally:
            pass
    def get_data(self, sql):
        # 查找数据
        print(sql)
        try:
            self.cursor.execute(sql)
            return self.cursor.fetchall()
        except Exception as e:
            print('Error: ', e)
        finally:
            pass
if __name__ == '__main__':
    conn = ConnectSqlserver(host='localhost', user="username", password="password",
                            db="databasename")
    sql = 'SELECT * FROM DATA1'
    data = conn.get_data(sql)
    print(data[-1][0])
```

> [pymssql - DB-API interface to Microsoft SQL Server](https://pypi.org/project/pymssql/)
