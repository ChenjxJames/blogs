---
title: Ubuntu16.04 Apache2 设置网站
date: 2018-11-09
categories:
- Server
tags:
- Ubuntu
- Apache2
---

### 开启ssl

```bash
sudo a2enmod ssl
```

### 开启rewrite

```bash
sudo a2enmod rewrite
```

### 设置虚拟主机

这里我以 `www.test.com` 为例。
在`/etc/apache2/sites-available/`下创建`test.conf`文件，进行以下配置。

<!-- more -->

```plaintext
<VirtualHost *:80>
    ServerName [www.test.com]
    ServerAdmin [管理人员邮箱]
    RewriteEngine on
    RewriteCond %{HTTPS} !=on
    RewriteRule ^(.*) https://%{SERVER_NAME}$1 [L,R=301]
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:443>
    ServerName [www.test.com]
    ServerAdmin [管理人员邮箱]
    DocumentRoot [/var/www/html/test/]
    SSLEngine on
    SSLCertificateFile /etc/apache2/ssl/2_[www.test.com].crt
    SSLCertificateKeyFile /etc/apache2/ssl/3_[www.test.com].key
    SSLCertificateChainFile /etc/apache2/ssl/1_root_bundle.crt
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

这里我们将 http 以 301 形式重定向到 https

### 启用站点

```bash
sudo a2ensite test.conf
```

### Apache2 重载配置文件

```bash
sudo service apache2 reload
```

之后我们的网站文件放在`/var/www/html/test/`下就可以访问了
