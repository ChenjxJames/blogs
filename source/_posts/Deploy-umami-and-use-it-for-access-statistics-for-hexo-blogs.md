---
title: Ubuntu 20.04 部署 umami 并用于 hexo 博客的访问统计
date: 2023-04-02 14:29:11
categories:
- Server
tags:
- umami
- hexo
- Ubuntu
---

## 什么是 umami

umami 是一个轻量级的网站访问统计工具，开源免费，可自托管，用途类似 Google Analytics。当然，umami 的功能远没有 Google Analytics 那么丰富，不过如果你的需求不多，只想了解网站的访问量（PV、UV）、流量来源等基本信息，以及记录一些简单的自定义事件，那么 umami 会是一个不错的选择。

<!-- more -->

## Ubuntu 部署 umami

### 创建所需的 mysql 用户及数据库

以 root 用户登录 mysql

```bash
mysql -u root -p
```

创建数据库 umami  
创建用户 umami_user 并授予其 umami 数据库的全部权限  
( 注: 请将 mypassword 改为你要设置的密码 )

```sql
CREATE DATABASE `umami` DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'umami_user'@'localhost' IDENTIFIED BY 'mypassword';
GRANT ALL PRIVILEGES ON `umami`.* TO 'umami_user'@'localhost';
```

### 拉取 umami 代码

```bash
git clone https://github.com/umami-software/umami.git
```

### 安装所需环境

* 运行 umami 需要版本大于 12 的 Node.js, 如果没有请按以下操作安装
  > 参考 [Node Version Manager](https://github.com/nvm-sh/nvm#installing-and-updating)

  ```bash
  sudo curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
  nvm install --lts
  ```

* 安装 yarn

  ```bash
  npm install -g yarn
  ```

* 安装依赖

  进入 umami 根目录, 运行如下命令安装依赖

  ```bash
  yarn install
  ```

### 创建配置文件

在 umami 根目录下创建配置文件 `.env` 并按如下格式配置  
( 注: 请将 mypassword 改为创建数据库用户 umami_user 时设置的密码 )

```env
DATABASE_URL=mysql://umami_user:mypassword@localhost:3306/umami
TRACKER_SCRIPT_NAME=analysis
```

### 编译代码

```bash
yarn build
```

### 运行

```bash
yarn start
```

看到如下输出就说明 umami 已经在 3000 端口运行成功了

```bash
$ next start -p 3000
ready - started server on 0.0.0.0:3000, url: http://localhost:3000
```

### 使用

* 首次登录的用户名和密码分别为 `admin` , `umami`
* 登录后修改密码避免安全隐患
* 如下图所示添加你的网站, 并获取跟踪代码, 将其加在网站的 `<head>` 标签中
![alt 添加网站](../images/umami-1.png)
![alt 添加网站](../images/umami-2.png)
![alt 获取跟踪代码](../images/umami-3.png)
* 查看统计结果
![alt 统计结果](../images/umami-4.png)

> 绑定域名可以参考 [Ubuntu 下使用 nginx 配置网站](/2023/04/01/nginx-config/)  
> [Umami Github](https://github.com/umami-software/umami)
