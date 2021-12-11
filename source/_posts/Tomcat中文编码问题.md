---
title: Tomcat 中文编码问题
date: 2018-10-24
categories:
- Code
- Java
tags:
- Java
- Tomcat
---

一般Tomcat默认的编码方式是ISO-8859-1，不支持中文。
在请求信息包含中文时会出现乱码问题。
解决方案如下：

<!-- more -->

## 前端

在HTML页面设置编码方式

```html
<meta charset="UTF-8">
```

## 后端

### POST

在 获取request参数前（即`request.getParameter();`前）加入以下代码设置解码方式为UTF-8。

```java
request.setCharacterEncoding("UTF-8");
```

在返回数据前（即`response.getWriter();`前）加入以下代码设置编码方式为UTF-8。

```java
response.setCharacterEncoding("UTF-8");
```

加入以下代码通知浏览器以UTF-8解码。

```java
response.setContentType("text/html;charset=UTF-8");
```

### GET

在 获取request参数后，对参数进行二次编码再解码。
如对searchVal进行二次编码再解码：

```java
searchVal = new String(searchVal.getBytes("ISO-8859-1"), "UTF-8");
```

Tomcat版本如果大于8则不需要进行此处理。
但GET请求中参数应尽量避免中文。

在返回数据前的处理和POST相同。
