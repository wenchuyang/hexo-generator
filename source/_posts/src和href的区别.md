---
title: src和href的区别
categories:
  - 计算机
  - 前端
  - HTML
tags:
  - 面试
date: 2022-12-01 21:07:57
---

# 定义
- src 指的是 source，用来内联一个额外的文件，比如 `<img src='a.png' />` 就是下载 a.png , 然后把它放到这个位置上来。
- href 全称是 hypertext reference, 超文本引用，一般是用来建立当前文档和外部文档的一个联系，相当于一个桥梁的作用。比如 `<a href='https://www.baidu.com'>上网</a>`, 点击这个超文本就能顺着它到外部文档上去。

# link 和 script
这两个标签分别是用来引入 css 和 js 的，分别用到了 href 和 src 属性，这里之所以拿出来单独讲是因为就我个人而言上面的定义没法说服我理解这两个标签。
讲之前先简单地提一下浏览器的渲染过程：加载 HTML -> 解析 HTML 以构建 DOM 树 -> 发请求下载 DOM 链接的资源 -> 确定渲染规则构建 render 树 -> 将 style 应用到 DOM 节点上(布局 render 树) -> 渲染页面(绘制 render 树)
在我们输入 URL 之后，浏览器会向服务器发送请求，服务器返回的一般是一个 HTML，浏览器拿到这个 HTML 文件之后开始做解析开始构建 DOM 树。浏览器是从上往下一行一行解析的，在遇到需要做资源请求的时候就继续向服务器发请求。
link 和 script 的不同点在于，link 发了请求之后就继续解析下一行，而 script 发了请求之后，就在等待请求的返回结果，然后执行 js 语句。执行完 js 语句之后，再去解析下一行。也因此如果内嵌 script 的话，最好是放在文档结尾，一般在 body 结束标签的上方（因为 body 标签下面的可能会被部分浏览器忽略）；而内嵌 style 的话都是放在文档头部，head 结束标签的上方。
但实际上我们在 html 里面写如下一段代码会发现链接的几个资源是同时下载的，并没有出现阻塞的情况。
```
<link rel="stylesheet" href="./styles.css" />
<script src="./index.js"></script>
<img src="1.jpg" />
<div id="app">111</div>
<script src="./index2.js"></script>
```
这是因为浏览器的预加载机制，在浏览器解析并构建 DOM 树的时候，预加载扫描仪同时扫描文档并请求高优先级资源，它的存在减少了 script 标签带来的阻塞。
但这并不影响它的主流程：解析 HTML 的时候遇到 script 标签会执行完里面的脚本再继续解析下一行。

## link
link 的特殊之处在于，虽然它使用的是 href，但是它是会主动下载它链接的资源的。相比之下 a 标签其实只是做了一个链接，并不会主动去发送请求。
另外想要提的一点就是 CSS 是渲染阻塞的：浏览器会阻塞页面渲染直到它接收和执行了所有的 CSS。CSS 是渲染阻塞是因为规则可以被覆盖，所以内容不能被渲染直到 CSSOM 的完成。但是它不阻塞 DOM 的加载，而且 DOM 的构建是一点一点往页面上 push 的。

## script
script 和 img 相比较而言，img 不会阻塞加载，只是做资源请求。在 HTML5 中，script 标签添加了两个属性：async 和 defer，可以用来控制是否做内容的阻塞。
如果添加了 async，那么会异步加载这个 script 引入的资源，在资源获取完成的时候执行脚本，与浏览器当前解析到哪里无关；
如果添加了 defer，那么会等到页面全部解析完成之后再去加载这个 script 引入的资源（在预加载的情况下会预加载，但还是会放到最后执行加载进来的 js ）；
如果都没有添加的话，就是前面说的先获取资源执行脚本，再解析下一行。
之前默认阻塞是因为 js 是可以操作 DOM 的，所以 js 的执行顺序其实是会影响页面布局，在添加了这两个属性之后相当于其中的风险由开发者控制，在没有必要的时候不需要让它阻塞流程。

# 参考资料
[stack overflow: difference between src and href](https://stackoverflow.com/questions/3395359/difference-between-src-and-href)
[MDN: 什么是 JavaScript](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/First_steps/What_is_JavaScript)
[MDN: 渲染页面：浏览器的工作原理](https://developer.mozilla.org/zh-CN/docs/Web/Performance/How_browsers_work)
[MDN: How CSS works](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps/How_CSS_works#how_does_css_actually_work)
[MDN: 关键渲染路径](https://developer.mozilla.org/zh-CN/docs/Web/Performance/Critical_rendering_path)


