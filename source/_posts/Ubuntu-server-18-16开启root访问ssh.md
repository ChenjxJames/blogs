---
title: Ubuntu server 18/16 开启 root 访问 ssh
date: 2019-11-09
categories:
- Server
tags:
- Ubuntu
- ssh
---

### 开启root用户

```bash
sudo passwd root
```

### 安装 openssh-server

```bash
sudo apt -y install openssh-server
```

<!-- more -->

### 编辑配置文件

```bash
sudo vi /etc/ssh/sshd_config
```

在行`#PermitRootLogin prohibit-password`后面换行添加以下内容

```plaintext
PermitRootLogin yes
```

### 启动服务

```bash
sudo service sshd start
```

注：不建议对公网开启root访问，有较大的安全隐患
