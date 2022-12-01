---
title: nvm 的安装使用
categories:
  - 计算机
tags:
  - nvm
  - 工具使用
date: 2022-12-01 14:00:09
---

# nvm 是什么
node version manager , node 版本管理器，是用来管理 node 版本的，让你可以在你的电脑上同时安装多个 node 版本。

# 下载安装 nvm
1. 上[nvm 官网](https://github.com/coreybutler/nvm-windows/releases)下载安装
2. 命令行输入 `nvm -v` 查看 nvm 是否安装成功
3. 在你的用户目录下新建 `.bash_profile` 文件：`touch ~/.bash_profile`。（如果有的话就打开文件。）
4. 在 `.bash_profile` 文件内写入：
    ```
    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
    ```
5. 命令行执行 `source ~/.bash_profile`，然后再看 nvm 有没有安装成功。

# 用 nvm 管理 node
1. `nvm list` 查看已安装的 node。
2. `nvm install v18.12.1` 安装指定版本的 node，此时如果报 permission denied 等权限问题就用管理员模式打开 git bash 再执行命令。
3. `nvm use 18.12.1` 使用指定版本 node，再次 ls 的时候使用的版本前面会带有一个 * 号。

# npm not found 的问题
1. 打开你的 nvm 安装地址，可以看见有带 node 版本号的文件夹。
2. 打开[node 下载地址](https://nodejs.org/en/download/releases/)，下载你需要的 node 版本。
3. 把你自己下载的 node 解压到对应版本号里面，然后再看你的 `npm -v` 能不能用。


# 参考资料
[Windows 上安装 NVM 之后找不到 NPM 的解决办法](https://www.cnblogs.com/jaxu/p/13904018.html)
[nvm：安裝、切換不同 Node.js 版本的管理器](https://titangene.github.io/article/nvm.html)