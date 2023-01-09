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

node version manager , node 版本管理器，是用来管理 node 版本的，让你可以在你的电脑上同时安装多个 node 版本，如果你不需要使用不同版本的 node ，那么没有必要去安装 nvm。

# 下载安装 nvm

1. 上[nvm 官网](https://github.com/coreybutler/nvm-windows/releases)下载安装包，指定路径安装，和其他的东西不同的是同时需要指定 node 的安装路径，因为可以通过 nvm 安装 node。
2. 命令行输入 `nvm -v` 查看 nvm 是否安装成功。
3. 如果上一步发现安装失败，在你的用户目录下新建 `.bash_profile` 文件：`touch ~/.bash_profile`。（如果有的话就打开文件。）
4. 在 `.bash_profile` 文件内写入：
   ```
   export NVM_DIR="$HOME/.nvm"
   [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
   ```
5. 命令行执行 `source ~/.bash_profile`，然后再看 nvm 有没有安装成功。

# 用 nvm 管理 node

1. `nvm list` 查看已安装的 node。
2. `nvm install v18.12.1` 安装指定版本的 node。这里在你安装 node 的时候会自动帮你安装对应版本的 npm，如果没有安装的话需要你自己在不同的 node 环境下安装不同版本的 npm。通过 [nodejs 官网](https://nodejs.org/zh-cn/download/releases/)你可以找到版本之间的对应关系。yarn 同理，但是我没有找到 yarn 和 node 的版本对应......
3. `nvm use 18.12.1` 使用指定版本 node，再次 ls 的时候使用的版本前面会带有一个 \* 号。这里直接修改可能会报错 `exit status 1: Access is denied` ，用管理员权限打开命令行即可解决问题。参考这篇[文章](https://sebhastian.com/node-nvm-exit-1-access-denied/)。

# npm not found 的问题

1. 打开你的 nvm 安装地址，可以看见有带 node 版本号的文件夹。
2. 打开[node 下载地址](https://nodejs.org/en/download/releases/)，下载你需要的 node 版本。
3. 把你自己下载的 node 解压到对应版本号里面，然后再看你的 `npm -v` 能不能用。

# 参考资料

[Windows 上安装 NVM 之后找不到 NPM 的解决办法](https://www.cnblogs.com/jaxu/p/13904018.html)
[nvm：安装、切换不同 Node.js 版本的管理器](https://titangene.github.io/article/nvm.html)
