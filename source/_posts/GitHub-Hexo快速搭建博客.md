---
layout: post
title: GitHub+Hexo快速搭建博客
date: 2018-01-05 11:01:48
tags: Hexo
categories:
- 计算机
---
Hexo 是一个快速、简洁且高效的博客框架。事实上，你也可以选择上[官网](https://hexo.io/zh-cn/)看教程。利用GitHub+Hexo我们可以快速搭建一个个人博客网站。以下是使用教程。
## 安装Hexo
&emsp;&emsp;据说windows下极易安装失败，但是我的win10并没有出现什么问题。所以我不知道你们会出现什么问题我也不会解决。<br>
&emsp;&emsp;如果你的系统里已经安装过Node.js和Git的话，安装Hexo会是一件再简单不过的事情。如果没有安装过的话，请自行百度教程。然后再继续本篇内容。<br>
&emsp;&emsp;打开git bash，然后运行`npm install -g hexo-cli`。然后就安装完成了。
## 在GitHub上建立项目
在GitHub上建立一个空的repo，名称是[你的用户名.github.io]，比如wenchuyang.github.io。
## 配置Hexo
是的安装完成之后你需要配置它，依旧用命令行。
1. 进入一个安全并且你能找得到的目录，比如`cd ~/Desktop`。
2. 执行以下命令
```
hexo init <folder>  //folder是你自己的文件夹名称，比如hexo init myBlog
cd <folder>
npm install  //或者简写为 npm i 
```
3. 您可以在_config.yml文件中修改大部分的配置。运行`start _config.yml`打开该文件，各参数如下。你需要把文件最后一行的type改成`type: git`，最后一行的后边新增一行，左边与type平齐，加上一行`repo: 仓库地址`（请将仓库地址改为「你的用户名.github.io」对应的仓库地址）。注意格式。其它请按照你自己的需求更改。
![](https://github.com/wenchuyang/ImagesForMarkdown/blob/809287088929455e79063cdfd6e94f344bc71661/images/703Y%25U0VDZA5@U9YJIT2.png?raw=true)

4. 安装git部署插件。`npm install hexo-deployer-git --save`
5. 部署网站。`hexo deploy`

## 写下你的第一篇blog
1. `hexo new 我的第一篇博客`
2. 复制显示的路径，使用`start 路径`来编辑它（windows给你显示的路径用的是"\"，你需要手动将其改成"/"才能进入目录。[微笑]）
3. `hexo generate`
4. `hexo deploy`
5. `hexo server`开启hexo服务，这样你就可以本地预览效果了。

## 切换主题
我猜你一定不会喜欢它默认的主题，当然如果你无所谓的话请忽略这一步。
1. [Hexo主题合集](https://github.com/hexojs/hexo/wiki/Themes)，找一个你喜欢的主题，进入主题的GitHub首页。
2. 复制它的SSH地址，假设地址是git@github.com:iissnan/hexo-theme-next.git
3. `cd themes`进入themes文件夹。
4. `git clone git@github.com:iissnan/hexo-theme-next.git`将项目克隆到你的本地themes文件夹
5. 
```
cd .. //退回到上层文件夹
start _config.yml //打开配置文件，将 _config.yml 的第 75 行改为 theme: hexo-theme-next，保存
hexo generate
hexo deploy
```
6. 等一分钟，然后刷新你的博客页面。

## 上传源码
上边我们建的仓库保存的只是你的博客页面，并没有保存源码。所以如果你误删了这个本地的文件夹.....嗯，为了防止你手抖做出什么可怕的事情，我们需要在GitHub上继续新建一个项目，用来保存生成博客的源码。
1. 在GitHub上创建blog-generator空仓库
2. 初始化空仓库，GitHub上有详细的步骤，一步一步来即可。不多说了。
温馨提示：你现在所在的目录应该是你的博客目录比如我的是myBlog，push到远程仓库的时候事实上你是将myBlog这整个的文件夹push上去。
3. 以后每次`hexo deploy`之后博客就会更新，然后你记着add/commit/push将blog-generator同步更新就好了。

## 同时部署到gitee上
1. `git deploy`的时候同时更新github和gitee：
   在`_config.yml`文件中仓库地址的位置添加
   ```
    deploy:
    - type: git
      repo: // 你的github对应的blog仓库地址
    - type: git
      repo: // 你的gitee对应的blog仓库地址
   ```
2. 添加公钥，不然会被拒绝访问。
3. 打开gitee页面预览
   打开你的仓库代码/服务/Gitee Pages，生成gitee page。
> 把你的gitee仓库名设置为你的用户名，这样生成的页面url是`https://用户名[.gitee.io/`，而且不会出现css不生效等系列问题。[官方文档](https://gitee.com/help/articles/4136#article-header0)