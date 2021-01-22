---
title: Hexo进阶使用指南
categories:
  - 计算机
tags: [Hexo]
date: 2021-01-09 13:31:57
---


# 初步使用
```
hexo new title.md
hexo generate
hexo server
hexo deploy
```
分别是新建、更新、预览、同步。
# _config.yml文件
每个theme会有一个单独的_config.yml文件，用于自定义主题。
而你的bolg generator下也会有一个全局的_config.yml文件，用于你自己博客的基础信息配置。可以逐项预览下来，这里只挑一些说。
# default_layout
默认布局，在`blog/_config.yml`文件里有设置。
有三种布局：post, page, draft。
其中draft是草稿，不会主动发布到网站，需要手动执行`hexo publish <title>`进行发布。要把文章存为草稿的话，从_post文件夹移到_draft文件夹。
在本地预览的时候，你可以把`render_drafts`设置为true，或者是`hexo server --draft`。
page是页面，会给你创建文件夹，以及文件夹目录中自动创建一个index.md文件。
`blog/source`文件夹内的_post和_draft文件夹是存放你的博客文档的，除了设置默认的layout之外，你还可以在创建文件的时候指定`hexo new post/draft test`。
# categories & tags
创建完文章之后打开文章，头部一般会有信息
```
---
title: Hexo进阶使用指南
date: 2021-01-09 13:31:57
---
```
你可以在里边加上categories和tags，下面两种方法都可以。
```
categories:
  - 计算机
  - 实用
tags: [Hexo, 计算机]
```
如果你只需要一个tag或者一个category可以像这样：
```
categories: 计算机
tags: 
  - Hexo
```
# scaffolds
默认模板。你可以在你的blog generator文件夹下找到`Scaffold`文件，里面存放的是新建文件时的默认模板。你可以在里边添加或修改一些东西。
