---
title: Ubuntu Server 18/16 安装 node.js 生产环境
date: 2019-11-10
categories:
- Server
tags:
- Ubuntu
- node.js
---


### 安装npm

```bash
sudo apt install npm
```

### 安装n模块（n模块专门用于管理node.js版本）

```bash
sudo npm install -g n
```

### 安装node.js

```bash
sudo n stable
```

<!-- more -->

### 安装pm2（node进程管理工具）

```bash
sudo npm install -g pm2
```

### 上传项目文件（这里我以一个开源的koa项目为例）

```bash
git clone https://github.com/TaleLin/lin-cms-koa.git starter
```

### 安装依赖

```bash
sudo npm install
```

### 配置数据库（app/config/secure.js ）

```javascript
module.exports = {
  db: {
    database: "lin-cms",
    host: "localhost",
    port: 3306,
    username: "root",
    password: "123456",
    logging: false
  }
};
```

### 启动项目

```bash
sudo pm2 start index.js --name uops
```

### 更改项目需要重启时

```bash
sudo pm2 delete uops
sudo pm2 start index.js --name uops -f
```
