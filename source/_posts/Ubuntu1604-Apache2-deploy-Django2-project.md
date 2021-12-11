---
title: Ubuntu16.04 Apache2 部署 Django2 项目
date: 2018-11-13
categories:
- Server
tags:
- Ubuntu
- Apache2
- Django
---

介绍一下环境: Ubuntu16.04、Apache2.4、Django2.1.2、python3.5.2、mysql

## 安装项目依赖

### 创建虚拟环境

为避免权限问题我将虚拟环境创建在`/home/ubuntu下`

```bash
sudo virtualenv -p /usr/bin/python3.5 /home/ubuntu/env_test
```

### 激活该虚拟环境

```bash
source /home/ubuntu/env_test/bin/activate
```

<!-- more -->

### 在此虚拟环境中安装所需的库

```bash
pip install -r package.txt
```

package.txt的格式如下（包名==版本号）:

```plaintext
Django==2.1.2
PyMySql==0.9.2
```

## 安装 mod_wsgi

这里我参考了[mod_wsgi官网安装教程](https://modwsgi.readthedocs.io/en/develop/user-guides/quick-installation-guide.html)

### 下载源码并解压

#### 在GitHub上下载.tar源代码并上传服务器`https://github.com/GrahamDumpleton/mod_wsgi/releases`

#### 在服务器端进行解压

  ```bash
  sudo tar xvfz mod_wsgi-X.Y.tar.gz
  ```

### 配置包

#### 在源代码目录中运行“configure”脚本

  ```bash
  sudo ./configure --with-apxs=/usr/local/apache/bin/apxs --with-python=/usr/local/bin/python
  ```

这里的apxs路径和python路径可以用whereis命名来查看，如查看python路径：

  ```bash
  whereis python
  ```

### 编译并安装

  ```bash
  sudo make
  sudo make install
  ```

### 开启wsgi

  ```bash
  sudo a2enmod wsgi
  ```

## 设置站点配置文件

这里我参考了[django官方文档](https://docs.djangoproject.com/zh-hans/2.1/howto/deployment/wsgi/modwsgi/)

在`/etc/apache2/sites-available/`下创建`test.conf`文件，做如下配置

```plaintext
<VirtualHost *:80>
    ServerAdmin [test@test.com]
    ServerName [test.com]

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    Alias /media/ /var/www/test/media/
    Alias /static/ /var/www/test/static/

    <Directory /var/www/test/media/>
        Require all granted
    </Directory>

    <Directory /var/www/test/static/>
        Require all granted
    </Directory>

    WSGIDaemonProcess Test python-home=/home/ubuntu/env_test python-path=/var/www/test
    WSGIProcessGroup Test
    WSGIScriptAlias / /var/www/test/test/wsgi.py
    <Directory /var/www/test/test/>
        <Files wsgi.py>
           Require all granted
        </Files>
    </Directory>
</VirtualHost>
```

## 启用站点

```bash
sudo a2ensite test.conf
sudo service apache2 reload
```

注意调整项目文件的权限，以免权限不足导致部分模块无法使用。

如有问题可以查看位于`/var/log/apache2/error.log`的错误日志
apache2的安装和配置请参考我的另外两篇文章。

> [Ubuntu 16.04 Apache2 设置网站](/2018/11/09/Ubuntu1604-Apache2-deploy-static-website/)
> [Ubuntu server 16.04 安装 Apache2](/2018/04/15/Ubuntu1604-install-apache2-python-mysql-phpmyadmin/)
