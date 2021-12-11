---
title: Windows10 使用 WSL ( Windows System Linux ) 子系统进行开发
date: 2021-07-13
tags:
- WSL
- Windows10
categories:
- Other
---

## 适用于 Linux 的 Windows 子系统 (WSL) 是 Windows 10 的一项功能，可用于直接在 Windows 上运行本机 Linux 命令行工具

---

### WSL优点

* Windows应用和Linux Terminal两者兼得，无缝切换
* 占用资源少（WSL 所需的资源（CPU、内存和存储）少于完整虚拟机所需的资源）
* 启动快（相比虚拟机WSL只需要启动一个Linux Terminal即可启动子系统，一般只需几秒钟）
* Linux相比Windows可以使用apt等包管理工具，安装环境更为便捷
* Windows可以直接操作WSL的用户目录（可以通过```\\wsl$\```访问WSL目录，并对用户目录```~\```有写权限）

---

<!-- more -->

### WSL Install 安装

1. 启用wsl功能及虚拟化，以管理员身份打开 PowerShell 并运行：

   ```terminal
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   ```

   ```terminal
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
   ```

2. 重启电脑
3. 下载[适用于 x64 计算机的 WSL2 Linux 内核更新包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)并安装
4. 设置WSL版本为WSL2

   ```terminal
   wsl --set-default-version 2
   ```

5. 通过[应用商店 Microsoft Store](https://aka.ms/wslstore)下载所需的Linux发行版
   * 如无法访问应用商店，可以通过[微软文档：手动下载适用于 Linux 的 Windows 子系统发行版包](https://docs.microsoft.com/zh-cn/windows/wsl/install-manual) 下载所需Linux发行版安装包手动安装
6. 运行所安装的Linux发行版进行初始化，设置用户密码
7. 安装所需环境
8. 在文件资源管理器中创建网络位置```\\wsl$\```便于访问WSL中的文件
9. 建议安装[Microsoft Terminal](https://github.com/microsoft/terminal/releases)并使用，其可以在多个选项卡 (在命令提示符、PowerShell 或多个 Linux 分发) 之间快速切换
10. 建议在WSL用户目录```~\```下进行项目开发

---

### FrontEnd Development 前端开发

1. Windows下安装[VS Code](https://code.visualstudio.com/)
2. VS Code安装[Remote-WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)或[Remote Development](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)插件即可直接对WSL进行“远程开发”
3. WSL安装nvm，最新版本可以到github查看[github/nvm](https://github.com/nvm-sh/nvm/releases)

   ```terminal
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
   ```

   ```terminal
   export NVM_DIR="$HOME/.nvm"
   ```

4. 安装所需版本的nodejs，如v14

   ```terminal
   nvm install v14
   ```

5. 配置git并创建ssh key：

   ```terminal
   cd ~
   mkdir .ssh
   cd ~/.ssh
   git config --global user.name "Your Name"
   git config --global user.email "YourEmail@example.com"
   ssh-keygen -trsa -C "YourEmail@example.com"
   cat id_rsa.pub
   ```

   * 复制ssh key并在github等平台配置

> [适用于 Linux 的 Windows 子系统文档（微软官方文档）](https://docs.microsoft.com/zh-cn/windows/wsl/)
