---
title: ECharts + Python3 Flask 实现数据实时监控
date: 2019-03-16
categories:
- Code
- Python
tags:
- Flask
---

## 思路

![项目架构](/images/04.png)

前端将已有数据中最大的id作为请求数据，

通过 `setInterval(function( ){ }, 1000);` 函数每间隔一秒向后端请求一次数据。

<!-- more -->

后端查找数据库中id大于前端返回的id的元数据并返回给前端。SQL 语句如下：

```sql
SELECT * FROM table_name WHERE ID > maxId ORDER BY ID;
```

## 前端

### 效果图

![数据实时监控效果图](/images/03.gif)

### 前端代码

```html
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <title>ECharts 数据实时监控</title>
    <script src="/static/js/jquery-3.2.1.min.js"></script>
    <script src="/static/js/echarts.js"></script>
</head>
<body>
<div style="">
    <h2 style="text-align: center;">数据实时监控 Demo</h2>
</div>
<div style="width: 1000px;height:500px; margin: auto;padding-top: 30px;">
    <div id="img_1" style="width: 500px;height:450px;float: left;"></div>
    <div id="img_2" style="width: 500px;height:450px;float: left;"></div>
</div>
<script>
    var maxId1 = 0;
    var maxId2 = 0;
    var data1 = [];
    var date1 = [];
    var data2 = [];
    var date2 = [];
    var myChart1 = echarts.init(document.getElementById('img_1'));
    var myChart2 = echarts.init(document.getElementById('img_2'));
    myChart1.setOption({
        title: {
            text: '数据实时监控1'
        },
        tooltip: {},
        legend: {
            data: ['数据1']
        },
        xAxis: {
            data: date1,
            axisLabel: {
                rotate: 45
            }
        },
        yAxis: {},
        series: [{
            name: '数据1',
            type: 'line',
            data: data1
        }]
    });
    myChart2.setOption({
        title: {
            text: '数据实时监控2'
        },
        tooltip: {},
        legend: {
            data: ['数据2']
        },
        xAxis: {
            data: date2,
            axisLabel: {
                rotate: 45
            }
        },
        yAxis: {},
        series: [{
            name: '数据2',
            type: 'line',
            data: data2
        }]
    });
    setInterval(function () {
        $.getJSON("/get_data/", {tableName: 'data1', maxId: maxId1}, function (data) {
            {#如果有新数据#}
            if (data.maxId > maxId1) {
                maxId1 = data.maxId;
                data1 = data1.concat(data.data);
                date1 = date1.concat(data.date);
                myChart1.setOption({
                    xAxis: {
                        data: date1
                    },
                    series: [{
                        // 根据名字对应到相应的系列
                        name: '数据1',
                        data: data1
                    }]
                });
            }
        });
        $.getJSON("/get_data/", {tableName: 'data2', maxId: maxId2}, function (data) {
            if (data.maxId > maxId2) {
                maxId2 = data.maxId;
                data2 = data2.concat(data.data);
                date2 = date2.concat(data.date);
                myChart2.setOption({
                    xAxis: {
                        data: date2
                    },
                    series: [{
                        // 根据名字对应到相应的系列
                        name: '数据2',
                        data: data2
                    }]
                });
            }
        });
    }, 1000);
</script>
</body>
</html>
```

## 后端

### 安装flask模块

```bash
pip install flask
```

### 后端代码

```python
from flask import Flask, render_template, url_for, request
from connect_DB.connect_sqlserver import *
import json
app = Flask(__name__)
@app.route('/')
def index():
    return render_template('index.html')
@app.route('/get_data/')
def get_data():
    """
    获取对应数据表的数据，以json形式返回
    :param table_name:
    :return:
    """
    table_name = request.args.get('tableName', 'DATA1')
    start_id = request.args.get('maxId', 0)
    # 创建数据库对象
    conn = ConnectSqlserver(host='111.231.56.167', user="chenjx_data_mining", password="Chenjx%456data",
                            db="data_mining")
    sql = 'SELECT * FROM ' + table_name + ' WHERE ID > ' + start_id + ' ORDER BY ID'
    data_list = []
    date_list = []
    max_id = start_id
    result = conn.get_data(sql)
    if result:
        max_id = result[-1][0]
        for item in result:
            date_list.append(item[2].strftime('%Y-%m-%d %H:%M:%S'))    # 将datetime转化成格式化字符串
            data_list.append(item[1])
    json_str = json.dumps({'data': data_list, 'date': date_list, 'maxId': max_id}, ensure_ascii=False)
    # print(json_str)
    return json_str
if __name__ == '__main__':
    app.debug = True
    app.run()
```

> 后端代码第二行的connect_DB.connect_sqlserver模块请参考: [python3使用pymssql连接SQL Server](/2019/03/15/python3-use-pymssql-connect-SQL-Server/)
> 相应的随机数据生成请参考: [Python3多线程向数据库插入随机数据](/2019/03/16/Python3-Multithreading-Thread/)
> [ECharts](https://echarts.apache.org/zh/index.html)
> [JQuery](https://jquery.com/)
