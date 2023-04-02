---
title: Ubuntu 20.04 下使用 nginx 配置网站
date: 2023-04-01 10:04:44
categories:
- Server
tags:
- nginx
- Ubuntu
---

## 安装 nginx

* 如果服务器上还没有安装 nginx 服务, 可以执行如下命令安装

```bash
sudo apt update
sudo apt install nginx
sudo service nginx start
```

## 静态站点

### 这里以站点 `www.test.cn` 为例讲解

* 首先将静态站点的文件放置在`/var/www/test`目录下

* 其次将该站点的ssl证书放置在`/etc/nginx/ssl/www.test.cn`目录下

* 在`/etc/nginx/sites-available`目录下新建站点配置文件 `www.test.cn`

  ```plaintext
  server {
      listen 80;
      listen [::]:80;

      server_name www.test.cn;

      rewrite ^(.*)$ https://${server_name}$1 permanent;
  }


  server {
      # SSL 访问端口号为 443
      listen 443 ssl;
      # 填写绑定证书的域名
      server_name www.test.cn;
      # 证书文件名称
      ssl_certificate /etc/nginx/ssl/www.test.cn/www.test.cn_bundle.crt;
      # 私钥文件名称
      ssl_certificate_key /etc/nginx/ssl/www.test.cn/www.test.cn.key;
      ssl_session_timeout 5m;
      # 请按照以下协议配置
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
      # 请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
      ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
      ssl_prefer_server_ciphers on;
      root /var/www/test;

      index index.html;

      location / {
          try_files $uri $uri/ =404;
      }
  }
  ```

* 建立软连接启用站点

  ```bash
  sudo ln -s /etc/nginx/sites-available/www.test.cn /etc/nginx/sites-enabled/www.test.cn
  ```

* 重启nginx使配置生效

  ```bash
  sudo service nginx restart
  ```

## 动态站点

* 运行站点程序，获得端口号

* 配置文件 `www.test.cn` 按以下格式配置，其他步骤同静态站点即可

  这里是将 `www.test.cn` 的请求转发到 3000 端口服务上，如你的服务端口不同, 改变该配置中的的端口号即可

  ```plaintext
  server {
      listen 80;
      listen [::]:80;

      server_name www.test.cn;

      rewrite ^(.*)$ https://${server_name}$1 permanent;
  }


  server {
      # SSL 访问端口号为 443
      listen 443 ssl;
      # 填写绑定证书的域名
      server_name www.test.cn;
      # 证书文件名称
      ssl_certificate /etc/nginx/ssl/www.test.cn/www.test.cn_bundle.crt;
      # 私钥文件名称
      ssl_certificate_key /etc/nginx/ssl/www.test.cn/www.test.cn.key;
      ssl_session_timeout 5m;
      # 请按照以下协议配置
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
      # 请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
      ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
      ssl_prefer_server_ciphers on;

      location / {
          proxy_pass http://localhost:3000/;  #转发目的（结尾带/ 则不将/baidu/ 放到转发的url中）
          proxy_connect_timeout 5s;    #超时时间
          proxy_set_header Host "localhost:3000";
          proxy_set_header X-Real-IP $remote_addr;
          proxy_set_header REMOTE-HOST $remote_addr;
          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
          # access_log  logs/access-baidu.log  main; # 转发日志输出
      }
  }
  ```
