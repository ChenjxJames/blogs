---
title: Ubuntu16.04 安装 Apache2 Python MySQL phpmyadmin
date: 2018-04-15
categories:
- Server
tags:
- Linux
- Ubuntu
---

ubuntu服务器配置的一些总结

#### 安装 python3, pip3

```bash
sudo apt install python3
sudo apt install python3-pip
```

#### 安装 mysql

```bash
sudo apt install mysql-server
sudo apt install libmysqlclient-dev
```

<!-- more -->

#### 装 phpmyadmin

一个mysql管理软件，通过网页访问，非常方便

```bash
sudo apt install php7.0
sudo apt install apache2
sudo apt install libapache2-mod-php7.0
sudo systemctl restart apache2
sudo apt install phpmyadmin
sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
```

#### 修改 php 配置文件

```bash
sudo vim /etc/php/7.0/apache2/php.ini
```

`display_errors = On`(显示错误日志，出现两次，都要改，不然无效)
`extension=php_mbstring.dll` (开启mbstring)

#### 重启 Apache

```bash
sudo /etc/init.d/apache2 restart
```

然后就可以通过浏览器URL=“【ip地址/域名】/phpmyadmin”访问了
