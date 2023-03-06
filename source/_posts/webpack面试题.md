---
title: webpack面试题
categories:
    - 计算机
    - 前端
tags:
    - webpack
    - 面试
date: 2023-03-06 13:57:00
---

# 说一说你对 webpack 的了解

webpack 是一个打包工具，它可以将我们的代码打包成浏览器可以识别的代码，通过 loaders 来翻译，通过 plugin 来做一些打包优化的工作。启动 webpack 服务之后它会做项目打包工作，然后再在本地启动一个服务来运行这个打包后的项目，让我们可以在本地查看代码效果。
webpack 的核心思想是模块化，在它看来，所有的文件都相当于一个模块，通过入口文件开始构建各个模块之间的依赖关系。

# webpack 有哪些构成，分别是用来做什么的？

1. 入口配置 (entry) : 从哪个文件开始构建
2. 出口配置 (output) : 最终构建出来的文件的输出路径
3. loaders (module.rules)：loader 承担着翻译的功能，webpack 只能识别 JavaScript 和 JSON 文件，在碰到其他类型的文件时通过 loader 的配置来做翻译工作。 test 用来匹配需要转换的文件，use 用来告诉 webpack 使用哪个 loader 来转换匹配到的文件。
4. plugins：webpack 插件，扩展 webpack 的功能，比如说打包优化。
5. mode：指定构建模式，development/production，指定当前构建是开发模式还是生产模式

# loader 和 plugin 的区别

loader 是翻译器，webpack 原生只支持 JavaScript 和 JSON，不同的 loader 可以翻译不同的语言，让 webpack 可以正常读取构建。
plugin 是插件，webpack 在构建的不同阶段会广播出一些事件，plugin 监听这些事件并进行处理，改变输出结果。

# 常见的 loader

sass-loader : 把 scss 转换为 css
css-loader : 解析 css
style-loader : 把 css 插入到 DOM 中
babel-loader : 把 ES6 转换成 ES5
ts-loader : 把 TypeScript 转换成 JavaScript
html-loader : 解析 html
file-loader : 复制资源文件并替换访问地址，在代码中使用相对 url 去引用输出的文件
url-loader : 除了 file-loader 的功能外，对于比较小的文件可以直接以 base64 的方式把文件内容注入到代码中

# 常见的 plugin

html-webpack-plugin: 在打包之后生成一个 html 文件来引用打包生成的 js 文件（webpack 的模块化，把每个文件都看做是一个模块，然后建立起这些模块之间的依赖关系）
imagemin-webpack-plugin: 压缩图片
clean-webpack-plugin: 每次打包前先清空输出文件夹再执行打包过程
commons-chunk-plugin: 提取公共代码

# webpack 的构建过程

1. 读取配置：根据配置文件以及 shell 命令拿到最终的配置。
2. 开始编译：根据配置文件加载 plugin，初始化 compile 对象，执行对象的 run 方法，开始编译。
3. 根据入口文件配置找到入口文件，开始构建依赖树。
4. 使用 loader 对读取到的依赖文件进行翻译，直到所有的文件翻译完成。
5. 拿到最终翻译好的依赖树。
6. 将所有翻译好的文件进行压缩打包，构建一个个 chunk，再转换成文件加入到输出列表。
7. 根据 output 配置将最终的打包结果写入文件系统。

# 如何提高构建速度

说一下大致的思路，提高构建速度可以结合构建过程来看，大致的过程如下：开始构建->翻译文件->压缩打包->写入到文件系统。
开始构建的时候主要是配置的读取，这里的速度取决于 webpack 本身，我们基本做不了什么。同样做不了什么的还有最后的写入文件步骤。
翻译文件阶段， webpack 在翻译的同时是在构建依赖树的，看入口文件依赖到哪个文件，然后找到对应的翻译器执行翻译。这个过程中一方面是查找对应的翻译器的过程，也许可以在写法上做优化，减少搜索时间；另一方面在 webpack 本身，如果能够支持多线程同时翻译多个文件的话也会大大提高此阶段的构建速度。
压缩打包阶段，同时压缩多个依赖文件同样可以提高构建速度。
除上述之外，跳出这个构建的流程，常用的一种优化是采用缓存。在第一次构建之后建立起缓存，在后续重复构建的过程中增量构建而不是全部构建，也可以大大提高构建速度。

# 转义出的文件过大怎么办

原始文件通过 loader 转义，如果转义出的文件过大，并且你使用的 loader 没有问题的话，那么需要考虑是不是原始文件太大了。
原始文件过大的话，需要分析代码了。一方面是提取出公共代码，另一方面是做功能的切分，单独的功能可以另外添加一个文件。

# 如何按需加载代码

webpack 提供了一种按需加载代码的方法：import(\*)，将你需要按需加载的文件通过 import 引入，这样在 webpack 解析的时候就会对这个文件重新生成一个 chunk，在浏览器加载代码的时候会在需要的时候再加载这个 chunk。
这个方法在实际应用中一般会结合路由来用，单页应用在运行的时候会先加载主要的 chunk，在需要展示某个页面的时候通过异步的方法按需加载组件对应的 chunk 。
除了上述方法外，组件库会提供组件的按需加载。在使用的时候单独引入你要用到的组件而不是一次性全部引入。

# webpack 热更新

## webpack 热更新的配置

在 webpack 配置文件中添加 `devServer.hot: true`，并且在 package.json 文件中添加 script 脚本 `"start": "webpack-dev-server --hot --open"`。
此时 devServer 会告诉 webpack 自动添加 HotModuleReplacementPlugin。
`{
        // ...
        devServer: {
            hot: true
        }
    }`

## 热更新原理

1. 启动 webpack ，本地访问页面。此时 webpack-dev-server 会启动两个服务，一个是 express 服务，主要用于提供静态资源，让浏览器可以直接解析打包后的资源；另一个是 socket 服务，在浏览器和服务器之间建立一个 socket 长连接，保证双方的通信。
2. webpack 通过 watch 监听本地代码的变动，如果有本地代码有更新，那么就重新编译，重新编译之后会产生两个文件：已改动模块的 manifest 文件；已改动模块的 chunk 文件。
3. devServer 通知浏览器有更新并发送当前编译的 hash 值。
4. 浏览器设置 `currentHash = hash` 改变当前的 hash 值。
5. HotModuleReplacement.runtime 的 hotCheck 方法会检查是否有更新。
6. 如果确认有更新的话向 devServer 发送 ajax 请求拿到 manifest 文件。（hotDownloadManifest）
7. 通过 JSONP 去请求更新的 chunk。(hotDownloadUpdateChunk)
8. 拿到 chunk 之后更新模块，检查依赖关系，更新依赖。
9. 如果热更新失败的话，直接进行 live-reload 刷新浏览器。

# webpack 的跨域解决方案

开发过程中使用 webpack 启动服务，会在本地的 localhost 开启一个端口，而此时与服务器进行通信的话就会存在同源问题。
webpack 自身提供了一个解决方案： proxy。
通过 proxy 配置代理服务器，代理服务器作为一个中间件，拦截本地向服务器端发送的请求之后，自己去向服务器发送请求；拿到服务器端的响应之后，再把响应的信息传给本地。从而实现跨域。

# 参考资料

-   [「吐血整理」再来一打 Webpack 面试题（构建流程）](https://juejin.cn/post/6844904094281236487#heading-3)
-   [关于 webpack 的面试题总结](https://zhuanlan.zhihu.com/p/44438844)
-   [玩转 webpack, 使你的打包速度提升 90%（提高构建速度的思路）](https://juejin.cn/post/6844904071736852487)
-   [webpack 总结(面试题简答)](https://zhuanlan.zhihu.com/p/354987097)
-   [Webpack——webpack 的编译原理（构建过程）](https://www.cnblogs.com/zhilili/p/14735077.html)
-   [Webpack HMR 原理解析( webpack 热更新)](https://zhuanlan.zhihu.com/p/30669007)
-   [webpack-hmr](https://github.com/careteenL/webpack-hmr)
-   [webpack 按需加载](https://webpack.wuhaolin.cn/4%E4%BC%98%E5%8C%96/4-12%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD.html)
