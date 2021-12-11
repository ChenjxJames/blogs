---
title: Python3 多线程 Thread
date: 2019-03-16
categories:
- Code
- Python
tags:
- Thread
---


多线程向数据库插入随机数据。

连接SQL server请参考：[python3 使用 pymssql 连接 SQL Server](/2019/03/15/python3-use-pymssql-connect-SQL-Server/)

启用两个线程分别向数据库‘databasename’中的表‘DATA1’和‘DATA2’插入随机数据。

<!-- more -->

```python
import random
import time
import threading
from connect_DB.connect_sqlserver import *
class MyThread(threading.Thread):
    def __init__(self, min, max, table_name, sleep_time):
        threading.Thread.__init__(self)
        self.conn = ConnectSqlserver(host='localhost', user="username", password="password", db="databasename")
        self.sleep_time = sleep_time
        self.table_name = table_name
        self.min = min
        self.max = max
    def run(self):  # 把要执行的代码写到run函数里面 线程在创建后会直接运行run函数
        while True:
            time.sleep(self.sleep_time)
            data = random.randint(self.min, self.max)
            sql = 'INSERT INTO ' + self.table_name + '([VALUE]) VALUES(' + str(data) + ');'
            self.conn.run_sql(sql)
def main():
    try:
        # 创建新线程
        thread1 = MyThread(100, 120, 'DATA1', 1)
        thread2 = MyThread(100, 120, 'DATA2', 1)
        # 开启线程
        thread1.start()
        thread2.start()
    except Exception as e:
        print('Error: ', e)
    while 1:
        pass
if __name__ == '__main__':
    main()
```

输出如下

```plaintext
...
INSERT INTO DATA1([VALUE]) VALUES(101);
INSERT INTO DATA1([VALUE]) VALUES(115);
INSERT INTO DATA2([VALUE]) VALUES(110);
INSERT INTO DATA1([VALUE]) VALUES(120);
INSERT INTO DATA2([VALUE]) VALUES(112);
INSERT INTO DATA1([VALUE]) VALUES(112);
INSERT INTO DATA2([VALUE]) VALUES(104);
...
```

> [threading --- 基于线程的并行](https://docs.python.org/zh-cn/3/library/threading.html)
