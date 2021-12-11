---
title: Ubuntu18.04 安装 GitLab
date: 2019-10-19
categories:
- Server
tags:
- Ubuntu
- GitLab
---

### 安装依赖项

```bash
sudo apt update
sudo apt install -y curl openssh-server ca-certificates
sudo apt install -y postfix
```

选择 Internet Site

### 安装 GitLab

#### 首先信任 GitLab 的 GPG 公钥

```bash
curl https://packages.gitlab.com/gpg.key 2> /dev/null | sudo apt-key add - &>/dev/null 
```

<!-- more -->

#### 设置清华大学安装源

```bash
sudo vi /etc/apt/sources.list.d/gitlab-ce.list
```

加入以下文字

```plaintext
deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu xenial main
```

#### 安装

```bash
sudo apt update
sudo apt install gitlab-ce
sudo gitlab-ctl reconfigure
sudo gitlab-ctl start
```

### 访问GitLab

[localhost](http://localhost)

> [Gitlab Ce | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror] (https://mirror.tuna.tsinghua.edu.cn/help/gitlab-ce/)
> [Ubuntu18.04 GitLab仓库服务器搭建](https://blog.csdn.net/EthanCo/article/details/82828097)
